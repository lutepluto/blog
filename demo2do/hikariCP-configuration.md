# HikariCP 配置初探

HikariCP 是又一款高性能 JDBC 连接池。按照官方的说法，HikariCP 是一款“零开销”，生产可用的 JDBC 连接池。并且它非常轻量，只有130Kb。根据官方提供的 HikariCP 与其他常用 JDBC 连接池做的基准测试中，HikariCP 确实表现卓越，性能远超其他 JDBC 连接池。

![JMH Benchmarks](https://github.com/brettwooldridge/HikariCP/wiki/HikariCP-bench-2.6.0.png)

HikariCP 的设计哲学推崇“简单可依赖”，这也正是这款连接池如此轻量的原因。对于开发者平日使用数据库连接池时面对最多的连接池配置项，HikariCP 跟其他流行的连接池相比也做到了“极简”，精简了配置项数量，特意的去掉了一些“多余的”配置项。

> Simplicity is prerequisite for reliability.
>
> -- <cite>Edsger Dijkstra</cite>

HikariCP 提供的默认配置在大部分情况下都能运行良好，且大部分配置项都是可选配置的。

#### 必需配置项

 - `dataSourceClassName`：接收字符串值，默认为空。
  JDBC driver 提供的 `DataSource` 类名。不同的 JDBC driver 会有其相对应的类名（不支持 XA data sources）。如果使用了 `jdbcUrl` 配置项则不需要配置此项。

 - `jdbcUrl`：接收字符串值，默认为空。
  此属性将使 HikariCP 使用“基于驱动管理器”（DriverManager-based）的配置。由于多种原因，我们认为基于数据源（DataSource-based）的配置是更好的选择。但对许多部署实例来讲却也区别不大。**当使用此属性来配置“旧”的 JDBC 驱动时，你可能也需要设置 `driverClassName` 属性，但可以试一试不设置是否能行得通。**

 - `username`：接收字符串值，默认为空。
  设置默认的获取数据库连接时需要验证的用户名。对 DataSource 来讲，username 仅会在调用 `DataSource.getConnection(username, password)` 时用到。但在使用基于驱动（Driver-based）配置时，HikariCP 会使用 `username` 的值去设置调用 `DriverManager.getConnection(jdbcUrl, props)` 方法时传入的 `Properties` 中的 `user` 属性。如果这并不是你想要的，你需要避免执行这个方法并调用 `addDataSourceProperty("username", ...)`。

 - `password`：接收字符串值，默认为空。
  设置默认的获取数据库连接时需要验证的密码。对 DataSource 来讲，password 仅会在调用 `DataSource.getConnection(username, passowrd)` 时用到。但在使用基于驱动（Driver-based）配置时，HikariCP 会使用 `password` 的值去设置调用 `DriverManager.getConnection(jdbcUrl, props)` 方法时传入的 `Properties` 中的 `password` 属性。如果这并不是你想要的，你需要避免执行这个方法并调用 `addDataSourceProperty("pass", ...)`。

#### 常用配置项

 - `autoCommit`：接收布尔值，默认为 `true`。
  控制从连接池中返回的连接的 auto-commit 行为。
  通常情况下，我们都会把 `autoCommit` 设置为 `false`。例如通常在 Spring 中，Spring 统一管理数据库事务，这时就需要禁用 auot-commit。

 - `connectionTimeout`：接收数值，默认为30000（30秒），最小可接收值为250ms。
  设置客户端获取连接前等待的最大毫秒数，即超时时间。如果超过了这个时间后仍然没有可用的数据库连接返回，`SqlException` 则会被抛出。

 - `idleTimeout`：接收数值，默认为600000（10分），最小可接收值为10000（10秒）。
  此属性控制一个连接保持空闲状态的最大超时时间。**只有当 `minimumIdle` 小于 `maximumPoolSize` 时此属性才会生效。**一个数据库连接是否退化为空闲状态需要平均15秒+，最多30秒+。设置0表示空闲的连接永远不会从连接池中销毁。

 - `maxLifetime`：接收数值，默认为1800000（30分）。
  此属性为单个连接在连接池中的最长生命周期时间。连接只有在被关闭后才会被移除。**强烈建议设置此属性，并且至少应该比任何数据库或组件强制要求的连接时间少30秒。**此属性设置为0表示没有最长生命周期时间。

 - `connectionTestQuery`：接收字符串值，默认为空。
  **如果数据库驱动支持 JDBC4，则强烈建议不要设置此属性。**此属性是为那些不支持 JDBC4 `Connection.isValid()` API 的老旧数据库准备的。这条查询语句会在连接从连接池返回给客户端之前执行，用以验证返回的数据库连接仍然可用。再次重申，在不设置此属性时尝试启动数据库连接池，如果你的数据库驱动不支持 JDBC4，HikariCP 会记录下错误信息。
  在 c3p0 中，这个属性的名称是 `preferredTestQuery`；在 tmocat-jdbc 中，这个属性的名称叫做 `validationQuery`。属性值一般设置为 `Select 1`。

 - `minimumIdle`：接收数值，默认和 `maximumPoolSize` 相同。
  设置 HikariCP 在连接池中保存的最少空闲连接数。如果空闲连接数少于此属性值，HikariCP 会尽力快速高效的增加连接。不过，为了最高性能和峰值弹性需求，我们建议不要设置此属性，而是让 HikariCP 作为一个固定大小的连接池。

 - `maximumPoolSize`：接收数值，默认为10。
  设置 HikariCP 在连接池中保存的最多连接数，包括空闲的和正在使用的连接。此属性的合理值应该由程序的运行环境决定。当连接池中没有空闲连接，调用 `getConnection()` 会一直阻塞直到超过 `connectionTimeout` 设置的超时时间。

 - `poolName`：接收字符串值，默认值为自动生成。
  此属性为连接池设置用户自定义的名称，并会在日志中显示。设置连接池名称主要是为了配合 JMX 在控制台日志中区分不同的连接池和连接池配置。注意，通过我们实践发现，如果需要配合使用 JMX，最好设置自定义的连接池名称。使用默认的自动生成的连接池名称有可能会出现意想不到的问题。

#### MySQL 配置优化

 - `prepStmtCacheSize`：接收数值，默认为25。
  此属性设置 MySQL 驱动在每个连接会缓存的 Prepared Statement 数量。推荐设为250到500之间。

 - `prepStmtCacheSqlLimit`：接收数值。
  此属性为 MySQL 驱动缓存的 Prepared SQL statement 的最大长度。MySQL 默认为256。根据我们的经验，尤其是在像 Hibernate 这样的 ORM 框架中，256的默认值远远小于生成的语句长度。我们推荐将其设置为2048。

 - `cachePrepStmts`：接收布尔值，默认为 `false`。
  打开预处理语句缓存。如果为 `false`，`prepStmtCacheSize` 和 `prepStmtCacheSqlLimit` 都不会起作用。

 - ~~`useServerPrepStmts`：接收布尔值。
  新版的 MySQL 支持服务端预处理语句，这可以极大的提高性能。如果支持，建议设置为 `true`。

一个典型的使用 HikariCP 的 MySQL 配置如下：

```
dataSourceClassName=com.mysql.jdbc.jdbc2.optional.MysqlDataSource
dataSource.url=jdbc:mysql://localhost/database
dataSource.user=test
dataSource.password=test
dataSource.cachePrepStmts=true
dataSource.prepStmtCacheSize=250
dataSource.prepStmtCacheSqlLimit=2048
```