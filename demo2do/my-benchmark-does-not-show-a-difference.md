# “我的基准测试并未显示有任何不同”

我们经常听到有希望使用 HikariCP 的用户表示“我的基准测试并未显示有任何不同”。

虽然 HikariCP 可能以快著称，但我们是花了很多功夫，在提供最高可靠性的限制条件下来达到这一速度的。我们相信 HikariCP 不仅仅是比其他连接池可靠一点，而是可靠的多。

用户们无法在 HikariCP 和其他连接池之间测试出不同的一个主要原因在于大多数连接池默认都运行在性能优于可靠性的模式下。

与此相反，HikariCP 没有“不安全的”运行模式——无法禁用“正确的”行为。一旦你打开其他连接池的可靠性开关来和 HikariCP 做对比，这些连接池的性能会显著下降。

在我们主页上引用的和其他连接池的[基准测试](https://raw.githubusercontent.com/wiki/brettwooldridge/HikariCP/Benchmarks.png)结果可能都过于客气了。为了只测量每个连接池占用的开销，我们的基准测试是跑在一个每项操作都运行空方法的残缺的 JDBC 驱动上（JDBC stub-driver）。当使用一个真正的 JDBC 驱动和数据库后，结果中差别可能会让人不敢相信。但请相信测试结果。

### 在一般比较当中有哪些不公平之处呢？

我们在这儿将讨论 HikariCP，Apache DBCP2 和 Tomcat DBCP，先谈及它们的速度，再引入可靠性。我们在一台真正的数据库（MySQL）上分别使用这个3个连接池跑了我们的基准测试。

**记住这里没有做任何查询操作，只是跑了 `getConnection()`，然后调用 `close()`。**

首先，3个连接池在默认配置下（加上autoCommit=false）的表现：

```
Benchmark                       (pool)  Mode   Score
ConnectionBench.cycleCnnection  hikari  thrpt  45289.116  ops/ms
ConnectionBench.cycleCnnection  tomcat  thrpt   2329.692  ops/ms
ConnectionBench.cycleCnnection  dbcp2   thrpt     21.750  ops/ms
```

#### DBCP2

DBCP2 因为在安全性方面的默认设置屡获加分，它的 `rollbackOnReturn` 默认设置为 `true`。

*这里没有展示出来的 DBCP2 会给数据库产生大约3MB每秒的流量，因为它会无条件的回滚。*

不幸的是，DBCP2 并不会检验返回的连接。

#### HikariCP

HikariCP 不给数据库产生任何流量。

HikariCP 也默认会“rollback on return”（这个选项并不能被关闭，因为这是连接池的正确行为），但是如果 SQL 语句已经被提交或者没有任何 SQL 语句运行，它会追踪事务状态而且不会回滚。

HikariCP 还会默认检验返回的连接（不能禁用此配置）。这里 HikariCP 使用了“如果连接在过去1000毫秒有任何操作，它就一定还存活着，所以可以通过连接校验”的优化措施。

#### Tomcat

Tomcat DBCP 也不会给数据库产生任何流量，但却另有原因。

*Tomcat 不仅完全不检验连接，并且也不会返回时回滚（rolling back on return）。*

***

现在，我们升级一下我们的测试，打开 Tomcat 和 DBCP 的连接校验配置。

```
Benchmark                       (pool)  Mode   Score
ConnectionBench.cycleCnnection  hikari  thrpt  45289.116  ops/ms
ConnectionBench.cycleCnnection  tomcat  thrpt   2133.992  ops/ms
ConnectionBench.cycleCnnection  dbcp2   thrpt      5.296  ops/ms
```

#### DBCP2

DBCP2 的表现严重滑坡。当然，它仍然给数据库产生大约每秒3MB的流量。

在`~5 ops/ms`状态下，TPS 最多能达到5000 TPS。而且别忘了还有3MB每秒的开销。

#### Tmocat

Tomcat 支持和 HikariCP 类似的优化，配置大致如下：

```
setTestOnBorrow(true);
setValidationInterval(1000);
setValidator( new Validator() {
   public boolean validate(Connection connection, int mode) {
      return connection.isValid(0);
   }
} );
```

结果如上所示效率大概为2133.992 ops/ms。仍然很好的表现，但在大多数基准测试中可能很难测量。

*但我们忘了 Tomcat 的“rollback on return”。打开这个配置后，其表现一落千丈:*

```
ConnectionBench.cycleCnnection  hikari  thrpt   45289.116  ops/ms
ConnectionBench.cycleCnnection  tomcat  thrpt      20.706  ops/ms
ConnectionBench.cycleCnnection  dbcp2   thrpt       5.296  ops/ms
```

Tomcat 现在也会向数据库产生大约每秒3MB的流量。它不会追踪事务状态因而会无条件回滚。我们本以为打开“连接状态追踪（ConnectionState tracking）”会有所帮助，但事实并非如此。

还有很多 HikariCP 的默认策略，包括[监测网络隔离](https://github.com/brettwooldridge/HikariCP/wiki/Bad-Behavior:-Handling-Database-Down)。

***

### 正确性（correctness）和可靠性（reliability）意味着什么？

在 SQL 连接和 JDBC 的范围内，正确性意味着一个理想的数据库连接池向应用返回的连接和刚刚从 JDBC 驱动处获取的“全新”连接别无二致。换句话来说，连接池对应用来讲是透明的，它的存在与否只会提升或降低性能，但并不会以任何方式改变应用的行为。

### Reset Connection State

ConnectionState* 拦截器在每次连接返回到连接池或连接创建时无条件获取／设置状态。

### Disposable Proxies

“Disposable Proxies”会阻止代码错误地使用返回了连接池的连接。没有它们线程就能破坏不相关联的事务。

### SQLException State Scanning

为 SQL-92 和 厂商特有的 断开连接代码检查 SQLException。DBCP2 对此项没有任何默认设置，所以这取决于用户来指定所有的断开连接代码。


