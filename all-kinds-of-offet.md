# 浏览器，HTML元素**高度／宽度／偏移**汇总

- **window.scrollX/window.scrollY**

  返回文档横向／纵向滚动了多少像素

  > For cross-browser compatibility, use window.pageYOffset instead of window.scrollY. Additionally, older versions of Internet Explorer (< 9) do not support either property and must be worked around by checking other non-standard properties. A fully compatible example:

  ```javascript
  var supportPageOffset = window.pageXOffset !== undefined;
  var isCSS1Compat = ((document.compatMode || "") === "CSS1Compat");

  var x = supportPageOffset ? window.pageXOffset : isCSS1Compat ? document.documentElement.scrollLeft : document.body.scrollLeft;
  var y = supportPageOffset ? window.pageYOffset : isCSS1Compat ? document.documentElement.scrollTop : document.body.scrollTop;
  ```

- **window.innerWidth/window.innerHeight**

  浏览器视窗像素宽度／高度，如果有滚动条包含滚动条

  > There is an [algorithm](https://bugzilla.mozilla.org/show_bug.cgi?id=189112#c7) to obtain the height of the viewport excluding, if rendered, the horizontal scrollbar.

- **window.outerWidth/window.outerHeight**

  浏览器窗口像素宽度／高度，包含浏览器工具栏，侧边栏等

  ![graphical example](https://developer.mozilla.org/@api/deki/files/213/=FirefoxInnerVsOuterHeight2.png)

- **[HTMLElement.offsetTop](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetTop)**

  当前元素相对于它的[offsetParent](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetParent)元素顶部的距离

  > The HTMLElement.offsetParent read-only property returns a reference to the object which is the closest (nearest in the containment hierarchy) positioned containing element. If the element is non-positioned, the nearest table cell or root element (html in standards compliant mode; body in quirks rendering mode) is the offsetParent. offsetParent returns null when the element has style.display set to "none". The offsetParent is useful because offsetTop and offsetLeft are relative to its padding edge.

- **[HTMLElement.offetLeft](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetLeft)**

  当前元素左上角相对于它的[offsetParent](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetParent)左边界像素偏移量

- **[HTMLElement.offsetHeight](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetHeight)**

  元素包含纵向内边距和边界的整数像素大小

  > This property will round the value to an integer. If you need a fractional value, use [element.getBoundingClientRect()](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect).

- **[HTMLElement.offsetWidth](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetWidth)**

  元素包含横向内边距和边界的像素大小值

- **[HTMLElement.offsetParent](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/offsetParent)**

  > The HTMLElement.offsetParent read-only property returns a reference to the object which is the closest (nearest in the containment hierarchy) positioned containing element. If the element is non-positioned, the nearest table cell or root element (html in standards compliant mode; body in quirks rendering mode) is the offsetParent. offsetParent returns null when the element has style.display set to "none". The offsetParent is useful because offsetTop and offsetLeft are relative to its padding edge.

- **[Element.clientWidth](https://developer.mozilla.org/en-US/docs/Web/API/Element/clientWidth)**

  元素包含内边距，但不包含外边距，边界和纵向滚动条的整数像素大小

- **[Element.clientHeight](https://developer.mozilla.org/en-US/docs/Web/API/Element/clientHeight)**

  元素包含内边距，但不包含外边距，边界和横向滚动条的整数像素大小

- **[Element.clientTop](https://developer.mozilla.org/en-US/docs/Web/API/Element/clientTop)**

  元素上边界宽度的像素值，不包含外边距和内边距

- **[Element.clientLeft](https://developer.mozilla.org/en-US/docs/Web/API/Element/clientLeft)**

  元素左边界宽度的像素值，不包含外边距和内边距。如果文字方向为从右向左，且包含纵向滚动条，则包含纵向滚动条的宽度。
