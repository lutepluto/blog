# [Javascript: The Definitive Guide](http://www.amazon.cn/JavaScript-The-Definitive-Guide-Flanagan-David/dp/0596805527/ref=sr_1_1?s=books&ie=UTF8&qid=1435031067&sr=1-1&keywords=javascript+the+definitive+guide)， Chapter 15 Scripting Documents

## Table of Contents

[Overview](#user-content-overview)
[Selecting Document Elements](#user-content-selecting-document-elements)
[Document Structure and Traversal](#user-content-document-structure-and-traversal)

## Overview

DOM是由很多不同节点（[Node](https://developer.mozilla.org/en-US/docs/Web/API/Node)）组成的。Node是一个顶级接口，其他很多DOM接口都继承于它，包括[Document](https://developer.mozilla.org/en-US/docs/Web/API/Document)，[Element](https://developer.mozilla.org/en-US/docs/Web/API/Element)，[CharacterData](https://developer.mozilla.org/en-US/docs/Web/API/CharacterData)（CharacterData是表示文字的节点，[Text](https://developer.mozilla.org/en-US/docs/Web/API/Text)，[Comment](https://developer.mozilla.org/en-US/docs/Web/API/Comment)，[CDATASection](https://developer.mozilla.org/en-US/docs/Web/API/CDATASection)都实现了CharacterData接口），[DocumentFragment](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment)等。

	![Partial Hierarchy of Document Nodes](images/partial-hierarchy-of-dom-nodes.png)
	
## Selecting Document Elements

DOM提供5种基本的方式来选择（获取）DOM元素：

- 通过指定的id获取
	
	```javascript
	var section1 = document.getElementById('#section1')
	```

	IE8之前，getElementById()是不区分大小写的且返回name属性值和制定id值相同的元素。

- 通过指定的name获取

	```javascript
	var radiobuttons = document.getElementsByName('favorite_color')
	```

	值得注意的是getElementsByName()是定义在[HTMLDocument](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDocument)类上，而并非Document类。因此此方法只在HTML文档里可用，在XML文档中不可用。

	不像id在文档中必须唯一的特性，很有可能拥有相同name的元素并不只有一个。所以getElementByName()返回是一个类似Array的NodeList对象。在IE中，getElementsByName()也会返回id为指定name的元素

- 通过指定的标签名（tag name）获取

	通过getElementsByTagName()可以选择任意指定标签名的HTML或XML元素。和getElementsByName()一样，它返回的事一个NodeList对象。

	获取文档中的所有元素

	```javascript
	var elements = document.getElementsByTageName('*')
	```

	在[Element](https://developer.mozilla.org/en-US/docs/Web/API/Element)接口和[Document](https://developer.mozilla.org/en-US/docs/Web/API/Document)一样，也定义了方法getElementsByTagName()。这意味着可以使用该方法来获取任意元素的子元素。

	```javascript
	var firstpara = document.getElementsByTagName('p')[0]
	var firstParaSpans = firstpara.getElementsByTagName('span')
	```

	历史原因，HTMLDocument可以直接访问某些类型的元素（images, forms, links）。这些属性返回的都是HTMLCollection对象。HTMLDOcument还定义了2个属性来表示单个元素，document.body和document.head。这两个属性总是被定义了的，如果文档源码没有包含`<head>`或`<body>`标签，浏览器会隐式的创建它们。docuemnt.documentElement表示文档根元素。在HTML文档中，就表示的`<html>`元素。

	> getElementsByName()和getElementsByTagname()返回的事NodeList对象，而document.images和docuemnt.forms则是HTMLCollection对象。这两个都是类Array的只读对象，都有length属性。非常重要的一点是NodeList和HTMLCollection所包含的元素是动态的，是会随着文档中的元素变化而改变的。

	> ```javascript
	// 通过Array的slice方法可以讲NodeList或HTMLCollection转为数组
	var paragraphs = Array.prototype.slice.call(docuemnt.getElementsByTagName('p'))
	```

- 通过指定一个或多个CSS类名获取

	Element.className, class is reserved word in javascript.

	document.getElementsByClassName() / Element.getElementsByClassName()

	```javascript
	// Find all elements that have "warning" in their class attribute
	var warnings = document.getElementsByClassName('warning')
	// Find all descendants of the element named "log" that have the class "error" and the class "fatal"
	var log = docuement.getElementById('log')
	// Class names are space separated and the order is irrelevant
	var fatal = log.getElementsByClassName('fatal error')
	```

	getElementsByClassName is not supported prior IE8.

- 通过匹配特定的CSS选择器

	document.querySelectorAll()/Element.querySelectorAll() returns NodeList which is not live.

	document.querySelector()/Element.querySelector() returns the first matching element or null if no matching element.

## Document Structure and Traversal

### Documents As Trees of Nodes

Node object defines following important properties:

property |
:--- | ---
parentNode | The node that is the parent of this one, or null for nodes like the Document objects that have no parent. |
childNodes | A read-only array-like object(a NodeList) that is a live representation of a Node's child nodes. |
firstChild, lastChild | The first and last child nodes of a node, or null if the node has no children. |
nextSibling, previousSibling | The next and previous sibling node of the node. Two nodes with the same parent are siblings. Their order reflects the order in which they appear in the document. These properties connect nodes in a doubly linked list. |
nodeType | The kind of node this is. [See detail here.](https://developer.mozilla.org/en-US/docs/Web/API/Node/nodeType) |
nodeValue | The textual content of a Text or Comment node. |
nodeName | The tag name of an Element, converted to uppercase. [See detail here.](https://developer.mozilla.org/en-US/docs/Web/API/Node/nodeName) |

### Documents As Trees of Elements

Not interested in Elements of a document instead of the text within them(and the whitespace between them). Ignoring Text and Comment node.

property |
:--- | ---
Element.children | Like childNodes, this is a NodeList. Unlike childNodes, the **children** contains only Element objects. Note that Text and Comment nodes cannot have children, which means that node.parentNode never returns a Text or Comment node. The parentNode of any Element will always be another Element, or, at the root of the tree, a Document or DocumentFragment.
firstElementChild, lastElmentChild | Like firstChild and lastChild, but for Element children only
nextElementSibling, previousElementSibling | Like nextSibling and previousSibling, but for Element siblings only
childElementCount | The number of element children, return the same value as children.length

These child and sibling properties are standardized and are implemented in all current browsers except IE.