> [Wiki](Home) ▸ [[API Reference]] ▸ [[Core]] ▸ **Selections**

A **selection** is an array of elements pulled from the current document. D3 uses [[CSS3|http://www.w3.org/TR/css3-selectors/]] to select elements. For example, you can select by tag  ("div"), class (".awesome"), unique identifier ("#foo"), attribute ("[color=red]"), or containment ("parent child"). Selectors can also be intersected (".this.that" for logical AND) or unioned (".this, .that" for logical OR). If your browser doesn't support selectors natively, you can include [[Sizzle|http://sizzlejs.com/]] before D3 for backwards-compatibility.

After selecting elements, you apply **operators** to them to do stuff. These operators can get or set [attributes](Selections#wiki-attr), [styles](Selections#wiki-style), [properties](Selections#wiki-property), [HTML](Selections#wiki-html) and [text](Selections#wiki-text) content. Attribute values and such are specified as either constants or functions; the latter are evaluated for each element. You can also join selections to [data](Selections#wiki-data); this data is available to operators for data-driven transformations. In addition, joining to data produces [enter](Selections#wiki-enter) and [exit](Selections#wiki-enter) subselections, so that you may [add](Selections#wiki-append) or [remove](Selections#wiki-remove) elements in response to changes in data.

You won't generally need to use `for` loops or recursive functions to modify the document with D3. That's because you operate on entire selections at once, rather than looping over individual elements. However, you can still loop over elements manually if you wish: there's an [each](Selections#wiki-each) operator which invokes an arbitrary function, and selections are arrays, so elements can be accessed directly (e.g., `selection[0][0]`). D3 supports method chaining for brevity when applying multiple operators: the operator return value is the selection.

## Selecting Elements

D3 provides two top-level methods for selecting elements: [select](Selections#wiki-d3_select) and [selectAll](Selections#wiki-d3_selectAll). These methods accept selector strings; the former selects only the first matching element, while the latter selects *all* matching elements in document traversal order. These methods can also accept nodes, which is useful for integration with third-party libraries such as jQuery or developer tools (`$0`).

<a name="d3_select" href="Selections#wiki-d3_select">#</a> d3.<b>select</b>(<i>selector</i>)

Selects the first element that matches the specified selector string, returning a single-element selection. If no elements in the current document match the specified selector, returns the empty selection. If multiple elements match the selector, only the first matching element (in document traversal order) will be selected.

<a href="Selections#wiki-d3_select">#</a> d3.<b>select</b>(<i>node</i>)

Selects the specified node. This is useful if you already have a reference to a node, such as `d3.select(this)` within an event listener, or a global such as `document.body`.

<a name="d3_selectAll" href="Selections#wiki-d3_selectAll">#</a> d3.<b>selectAll</b>(<i>selector</i>)

Selects all elements that match the specified selector. The elements will be selected in document traversal order (top-to-bottom). If no elements in the current document match the specified selector, returns the empty selection.

<a href="Selections#wiki-d3_selectAll">#</a> d3.<b>selectAll</b>(<i>nodes</i>)

Selects the specified array of elements. This is useful if you already have a reference to nodes, such as `d3.selectAll(this.childNodes)` within an event listener, or a global such as `document.links`. The *nodes* argument doesn't have to be an array, exactly; any pseudo-array that can be coerced into an array (e.g., a `NodeList` or `arguments`) will work.

## Operating on Selections

Selections are arrays of elements—literally ([maybe not literally...](http://bost.ocks.org/mike/selection/#subclass)). D3 binds additional methods to the array so that you can apply operators to the selected elements, such as setting an attribute on all the selected elements. One nuance is that selections are *grouped*: rather than a one-dimensional array, each selection is an *array of arrays* of elements. This preserves the hierarchical structure of subselections. Most of the time, you can ignore this detail, but that's why a single-element selection looks like `[[node]]` rather than `[node]`. For more on nested selections, see [Nested Selections](http://bost.ocks.org/mike/nest/).

If you want to learn how selections work, try selecting elements interactively using your browser's developer console. You can inspect the returned array to see which elements were selected, and how they are grouped. You can also then apply operators to the selected elements and see how the page content changes.

### Content

D3 has a variety of operators which affect the document content. These are what you'll use the most to display data! When used to set document content, the operators return the current selection, so you can chain multiple operators together in a concise statement.

<a name="attr" href="Selections#wiki-attr">#</a> selection.<b>attr</b>(<i>name</i>[, <i>value</i>])

If *value* is specified, sets the attribute with the specified name to the specified value on all selected elements. If *value* is a constant, then all elements are given the same attribute value; otherwise, if *value* is a function, then the function is evaluated for each selected element (in order), being passed the current datum `d` and the current index `i`, with the `this` context as the current DOM element. The function's return value is then used to set each element's attribute. A null value will remove the specified attribute.

If *value* is not specified, returns the value of the specified attribute for the first non-null element in the selection. This is generally useful only if you know that the selection contains exactly one element.

The specified *name* may have a namespace prefix, such as `xlink:href`, to specify an "href" attribute in the XLink namespace. By default, D3 supports svg, xhtml, xlink, xml, and xmlns namespaces. Additional namespaces can be registered by adding to [d3.ns.prefix](Namespaces#wiki-prefix).

*name* can also be an Object of *name* and *value* attributes.

<a name="classed" href="Selections#wiki-classed">#</a> selection.<b>classed</b>(<i>name</i>[, <i>value</i>])

This operator is a convenience routine for setting the "class" attribute; it understands that the "class" attribute is a set of tokens separated by spaces. Under the hood, it will use the [[classList|https://developer.mozilla.org/en/DOM/element.classList]] if available, for convenient adding, removing and toggling of CSS classes.

If *value* is specified, sets whether or not the specified class is associated with the selected elements. If *value* is a constant and truthy, then all elements are assigned the specified class, if not already assigned; if falsey, then the class is removed from all selected elements, if assigned. If *value* is a function, then the function is evaluated for each selected element (in order), being passed the current datum `d` and the current index `i`, with the `this` context as the current DOM element. The function's return value is then used to assign or unassign the specified class on each element.

If you want to set several classes at once, use an object literal like so: `selection.classed({'foo': true, 'bar': false})`.

If *value* is not specified, returns true if and only if the first non-null element in this selection has the specified class. This is generally useful only if you know the selection contains exactly one element.

<a name="style" href="Selections#wiki-style">#</a> selection.<b>style</b>(<i>name</i>[, <i>value</i>[, <i>priority</i>]])

If *value* is specified, sets the CSS style property with the specified name to the specified value on all selected elements. If *value* is a constant, then all elements are given the same style value; otherwise, if *value* is a function, then the function is evaluated for each selected element (in order), being passed the current datum `d` and the current index `i`, with the `this` context as the current DOM element. The function's return value is then used to set each element's style property. A null value will remove the style property. An optional *priority* may also be specified, either as null or the string "important" (without the exclamation point).

If you want to set several style properties at once, use an object literal like so: `selection.style({'stroke': 'black', 'stroke-width': 2})`

If *value* is not specified, returns the current *computed* value of the specified style property for the first non-null element in the selection. This is generally useful only if you know the selection contains exactly one element. Note that the computed value may be *different* than the value that was previously set, particularly if the style property was set using a shorthand property (such as the "font" style, which is shorthand for "font-size", "font-face", etc.).

<a name="property" href="Selections#wiki-property">#</a> selection.<b>property</b>(<i>name</i>[, <i>value</i>])

Some HTML elements have special properties that are not addressable using standard attributes or styles. For example, form text fields have a `value` string property, and checkboxes have a `checked` boolean property. You can use the `property` operator to get or set these properties, or any other addressable field on the underlying element, such as `className`.

If *value* is specified, sets the property with the specified name to the specified value on all selected elements. If *value* is a constant, then all elements are given the same property value; otherwise, if *value* is a function, then the function is evaluated for each selected element (in order), being passed the current datum `d` and the current index `i`, with the `this` context as the current DOM element. The function's return value is then used to set each element's property. A null value will delete the specified attribute.

If you want to set several properties at once, use an object literal like so: `selection.properties({'foo': 'bar', 'baz': 'qux'})`.

If *value* is not specified, returns the value of the specified property for the first non-null element in the selection. This is generally useful only if you know the selection contains exactly one element.

<a name="text" href="Selections#wiki-text">#</a> selection.<b>text</b>([<i>value</i>])

The `text` operator is based on the [[textContent|http://www.w3.org/TR/DOM-Level-3-Core/core.html#Node3-textContent]] property; setting the text content will replace any existing child elements.

If *value* is specified, sets the text content to the specified value on all selected elements. If *value* is a constant, then all elements are given the same text content; otherwise, if *value* is a function, then the function is evaluated for each selected element (in order), being passed the current datum `d` and the current index `i`, with the `this` context as the current DOM element. The function's return value is then used to set each element's text content. A null value will clear the content.

If *value* is not specified, returns the text content for the first non-null element in the selection. This is generally useful only if you know the selection contains exactly one element.

<a name="html" href="Selections#wiki-html">#</a> selection.<b>html</b>([<i>value</i>])

The `html` operator is based on the [[innerHTML|http://www.w3.org/TR/html5/apis-in-html-documents.html#innerhtml]] property; setting the inner HTML content will replace any existing child elements. Also, you may prefer to use the `append` or `insert` operators to create HTML content in a data-driven way; this operator is intended for when you want a little bit of HTML, say for rich formatting.

If *value* is specified, sets the inner HTML content to the specified value on all selected elements. If *value* is a constant, then all elements are given the same inner HTML content; otherwise, if *value* is a function, then the function is evaluated for each selected element (in order), being passed the current datum `d` and the current index `i`, with the `this` context as the current DOM element. The function's return value is then used to set each element's inner HTML content. A null value will clear the content.

If *value* is not specified, returns the inner HTML content for the first non-null element in the selection. This is generally useful only if you know the selection contains exactly one element.

Note: as its name suggests, selection.html is only supported on HTML elements. SVG elements and other non-HTML elements do not support the innerHTML property, and thus are incompatible with selection.html. Consider using [XMLSerializer](https://developer.mozilla.org/en-US/docs/XMLSerializer) to convert a DOM subtree to text. See also the [innersvg polyfill](https://code.google.com/p/innersvg/), which provides a shim to support the innerHTML property on SVG elements.

<a name="append" href="Selections#wiki-append">#</a> selection.<b>append</b>(<i>name</i>)

Appends a new element with the specified *name* as the last child of each element in the current selection, returning a new selection containing the appended elements. Each new element inherits the data of the current elements, if any, in the same manner as [select](Selections#wiki-select) for subselections.

The *name* may be specified either as a constant string or as a function that returns the DOM element to append. When the *name* is specified as a string, it may have a namespace prefix of the form "namespace:tag". For example, "svg:text" will create a "text" element in the SVG namespace. By default, D3 supports svg, xhtml, xlink, xml and xmlns namespaces. Additional namespaces can be registered by adding to [d3.ns.prefix](Namespaces#wiki-prefix). If no namespace is specified, then the namespace will be inherited from the enclosing element; or, if the name is one of the known prefixes, the corresponding namespace will be used (for example, "svg" implies "svg:svg").

<a name="insert" href="Selections#wiki-insert">#</a> selection.<b>insert</b>(<i>name</i>[, <i>before</i>])

Inserts a new element with the specified *name* before the element matching the specified *before* selector, for each element in the current selection, returning a new selection containing the inserted elements. If the before selector does not match any elements, then the new element will be the last child as with [append](Selections#wiki-append). Each new element inherits the data of the current elements (if any), in the same manner as [select](Selections#wiki-select) for subselections.

The *name* may be specified either as a constant string or as a function that returns the DOM element to append. When the *name* is specified as a string, it may have a namespace prefix of the form "namespace:tag". For example, "svg:text" will create a "text" element in the SVG namespace. By default, D3 supports svg, xhtml, xlink, xml and xmlns namespaces. Additional namespaces can be registered by adding to [d3.ns.prefix](Namespaces#wiki-prefix). If no namespace is specified, then the namespace will be inherited from the enclosing element; or, if the name is one of the known prefixes, the corresponding namespace will be used (for example, "svg" implies "svg:svg").

Likewise, the *before* selector may be specified as a selector string or a function which returns a DOM element. For instance, `insert("div", ":first-child")` will prepend child div nodes to the current selection. For [enter selections](Selections#wiki-enter), the *before* selector may be omitted, in which case entering elements will be inserted immediately before the next following sibling in the update selection, if any. This allows you to insert elements into the DOM in an order consistent with bound data. Note, however, the slower [selection.order](Selections#wiki-order) may still be required if updating elements change order.

<a name="remove" href="Selections#wiki-remove">#</a> selection.<b>remove</b>()

Removes the elements in the current selection from the current document. Returns the current selection (the same elements that were removed) which are now “off-screen”, detached from the DOM. Note that there is not currently a dedicated API to add removed elements back to the document; however, you can pass a function to selection.append or selection.insert to re-add elements.

### Data

<a name="data" href="Selections#wiki-data">#</a> selection.<b>data</b>([<i>values</i>[, <i>key</i>]])

Joins the specified array of data with the current selection. The specified *values* is an array of data values, such as an array of numbers or objects, or a function that returns an array of values. If a *key* function is not specified, then the first datum in the specified array is assigned to the first element in the current selection, the second datum to the second selected element, and so on. When data is assigned to an element, it is stored in the property `__data__`, thus making the data "sticky" so that the data is available on re-selection.

The *values* array specifies the data **for each group** in the selection. Thus, if the selection has multiple groups (such as a [d3.selectAll](#wiki-d3_selectAll) followed by a [selection.selectAll](#wiki-selectAll)), then *data* should be specified as a function that returns an array (assuming that you want different data for each group). For example, you may bind a two-dimensional array to an initial selection, and then bind the contained inner arrays to each subselection. The *values* function in this case is the identity function: it is invoked for each group of child elements, being passed the data bound to the parent element, and returns this array of data.

```javascript
var matrix = [
  [11975,  5871, 8916, 2868],
  [ 1951, 10048, 2060, 6171],
  [ 8010, 16145, 8090, 8045],
  [ 1013,   990,  940, 6907]
];

var tr = d3.select("body").append("table").selectAll("tr")
    .data(matrix)
  .enter().append("tr");

var td = tr.selectAll("td")
    .data(function(d) { return d; })
  .enter().append("td")
    .text(function(d) { return d; });
```

To control how data is joined to elements, a *key* function may be specified. This replaces the default by-index behavior; the key function is invoked once for each element in the new data array, and once again for each existing element in the selection. In both cases the key function is passed the datum `d` and the index `i`. When the key function is evaluated on new data elements, the `this` context is the data array; when the key function is evaluated on the existing selection, the `this` context is the associated DOM element. The key function returns a string which is used to join a datum with its corresponding element, based on the previously-bound data. For example, if each datum has a unique field `name`, the join might be specified as:

```javascript
.data(data, function(d) { return d.name; })
```

For a more detailed example of how the key function affects the data join, see the tutorial [[A Bar Chart, Part 2|http://mbostock.github.com/d3/tutorial/bar-2.html]].

The result of the `data` operator is the *update* selection; this represents the selected DOM elements that were successfully bound to the specified data elements. The *update* selection also contains a reference to the [enter](Selections#wiki-enter) and [exit](Selections#wiki-exit) selections, for adding and removing nodes in correspondence with data. For example, if the default by-index key is used, and the existing selection contains fewer elements than the specified data, then the *enter* selection will contain placeholders for the new data. On the other hand, if the existing contains more elements than the data, then the *exit* selection will contain the extra elements. And, if the existing selection exactly matches the data, then both the enter and exit selections will be empty, with all nodes in the update selection. For more details, see the short tutorial [Thinking With Joins](http://bost.ocks.org/mike/join/).

If a key function is specified, the `data` operator also affects the index of nodes; this index is passed as the second argument `i` to any operator function values. However, note that existing DOM elements are not automatically reordered; use [sort](#wiki-sort) or [order](#wiki-order) as needed.

If *values* is not specified, then this method returns the array of data for the first group in the selection. The length of the returned array will match the length of the first group, and the index of each datum in the returned array will match the corresponding index in the selection. If some of the elements in the selection are null, or if they have no associated data, then the corresponding element in the array will be undefined.

Note: the `data` method cannot be used to clear previously-bound data; use [selection.datum](#wiki-datum) instead.

<a name="enter" href="Selections#wiki-enter">#</a> selection.<b>enter()</b>

Returns the entering selection: placeholder nodes for each data element for which no corresponding existing DOM element was found in the current selection. This method is only defined on a selection returned by the [data](Selections#wiki-data) operator. In addition, the entering selection only defines [append](Selections#wiki-append), [insert](Selections#wiki-insert), [select](Selections#wiki-select) and [call](Selections#wiki-call) operators; you must use these operators to instantiate the entering nodes before modifying any content. (Enter selections also support [empty](Selections#wiki-empty) to check if they are empty.) Note that the *enter* operator merely returns a reference to the entering selection, and it is up to you to add the new nodes.

As a simple example, consider the case where the existing selection is empty, and we wish to create new nodes to match our data:

```javascript
d3.select("body").selectAll("div")
    .data([4, 8, 15, 16, 23, 42])
  .enter().append("div")
    .text(function(d) { return d; });
```

Assuming that the body is initially empty, the above code will create six new DIV elements, append them to the body in order, and assign their text content as the associated (string-coerced) number:

```html
<div>4</div>
<div>8</div>
<div>15</div>
<div>16</div>
<div>23</div>
<div>42</div>
```

Another way to think about the entering placeholder nodes is that they are pointers to the parent node (in this example, the document body); however, they only support append and insert.

The enter selection **merges into the update selection** when you append or insert. This approach reduces code duplication between enter and update. Rather than applying operators to both the enter and update selection separately, you can now apply them to the update selection after entering the nodes. In the rare case that you want to run operators only on the
updating nodes, you can run them on the update selection before entering new nodes.

<a name="exit" href="Selections#wiki-exit">#</a> selection.<b>exit()</b>

Returns the exiting selection: existing DOM elements in the current selection for which no new data element was found. This method is only defined on a selection returned by the [data](Selections#wiki-data) operator. The exiting selection defines all the normal operators, though typically the main one you'll want to use is [remove](Selections#wiki-remove); the other operators exist primarily so you can define an exiting transition as desired. Note that the *exit* operator merely returns a reference to the exiting selection, and it is up to you to remove the new nodes.

As a simple example, consider updating the six DIV elements created in the above example for the enter operator. Here we bind those elements to a new array of data with some new and some old:

```javascript
var div = d3.select("body").selectAll("div")
    .data([1, 2, 4, 8, 16, 32], function(d) { return d; });
```

Now `div`—the result of the data operator—refers to the updating selection. Since we specified a key function using the identity function, and the new data array contains the numbers [4, 8, 16] which also exist in the old data array, this updating selection contains three DIV elements. Let's say we leave those elements as-is. We can instantiate and add the new elements [1, 2, 32] using the entering selection:

```javascript
div.enter().append("div")
    .text(function(d) { return d; });
```

Likewise, we can remove the exiting elements [15, 23, 42]:

```javascript
div.exit().remove();
```

Now the document body looks like this:

```html
<div>4</div>
<div>8</div>
<div>16</div>
<div>1</div>
<div>2</div>
<div>32</div>
```

Note that the DOM elements are now out-of-order. However, the selection index `i` (the second argument to operator functions), will correctly match the new data array. For example, we could assign an index attribute:

```javascript
d3.selectAll("div").attr("index", function(d, i) { return i; });
```

This would result in:

```html
<div index="2">4</div>
<div index="3">8</div>
<div index="4">16</div>
<div index="0">1</div>
<div index="1">2</div>
<div index="5">32</div>
```

If you want the document traversal order to match the selection data order, you can use [sort](#wiki-sort) or [order](#wiki-order).

<a name="filter" href="Selections#wiki-filter">#</a> selection.<b>filter</b>(<i>selector</i>)

Filters the selection, returning a new selection that contains only the elements for which the specified *selector* is true. The *selector* may be specified either as a function or as a selector string, such as ".foo". As with other operators, the function is passed the current datum `d` and index `i`, with the `this` context as the current DOM element. Like the built-in array [[filter|https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Array/Filter]] method, the returned selection *does not* preserve the index of the original selection; it returns a copy with elements removed. If you want to preserve the index, use [select](Selections#wiki-select) instead. For example, to select every odd element (relative to the zero-based index):

```javascript
var odds = selection.select(function(d, i) { return i & 1 ? this : null; });
```

Equivalently, using a filter function:

```javascript
var odds = selection.filter(function(d, i) { return i & 1; });
```

Or a filter selector (noting that the :nth-child pseudo-class is a one-based index rather than a zero-based index):

```javascript
var odds = selection.filter(":nth-child(even)");
```

Thus, you can use either select or filter to apply operators to a subset of elements.

<a name="datum" href="Selections#wiki-datum">#</a> selection.<b>datum</b>([<i>value</i>])

Gets or sets the bound data for each selected element. Unlike the [selection.data](#wiki-data) method, this method does not compute a join (and thus does not compute enter and exit selections). This method is implemented on top of [selection.property](#wiki-property):

```js
d3.selection.prototype.datum = function(value) {
  return arguments.length < 1
      ? this.property("__data__")
      : this.property("__data__", value);
};
```

If *value* is specified, sets the element's bound data to the specified value on all selected elements. If *value* is a constant, all elements are given the same data; otherwise, if *value* is a function, then the function is evaluated for each selected element, being passed the previous datum `d` and the current index `i`, with the `this` context as the current DOM element. The function is then used to set each element's data. A null value will delete the bound data. This operator has no effect on the index.

If *value* is not specified, returns the bound datum for the first non-null element in the selection. This is generally useful only if you know the selection contains exactly one element.

Note: this method was previously called "map". The old name is deprecated.

The `datum` method is useful for accessing HTML5 [custom data attributes](http://www.w3.org/TR/html5/dom.html#custom-data-attribute) with D3. For example, given the following elements:

```html
<ul id="list">
  <li data-username="shawnbot">Shawn Allen</li>
  <li data-username="mbostock">Mike Bostock</li>
</ul>
```

You can expose the custom data attributes to D3 by setting each element’s data as the built-in [dataset](http://www.w3.org/TR/html5/dom.html#dom-dataset) property:

```javascript
selection.datum(function() { return this.dataset; })
```

This can then be used, [for example](http://bl.ocks.org/1323729), to sort elements by username.

<a name="sort" href="#wiki-sort">#</a> selection.<b>sort</b>(<i>comparator</i>)

Sorts the elements in the current selection according to the specified comparator function. The comparator function is passed two data elements *a* and *b* to compare, returning either a negative, positive, or zero value. If negative, then *a* should be before *b*; if positive, then *a* should be after *b*; otherwise, *a* and *b* are considered equal and the order is arbitrary. Note that the sort is not guaranteed to be stable; however, it is guaranteed to have the same behavior as your browser's built-in [[sort|https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Array/sort]] method on arrays.

<a name="order" href="#wiki-order">#</a> selection.<b>order</b>()

Re-inserts elements into the document such that the document order matches the selection order. This is equivalent to calling sort() if the data is already sorted, but much faster.

### Animation & Interaction

<a name="on" href="Selections#wiki-on">#</a> selection.<b>on</b>(<i>type</i>[, <i>listener</i>[, <i>capture</i>]])

Adds or removes an event *listener* to each element in the current selection, for the specified *type*. The *type* is a string event type name, such as "click", "mouseover", or "submit".  The specified *listener* is invoked in the same manner as other operator functions, being passed the current datum `d` and index `i`, with the `this` context as the current DOM element. To access the current event within a listener, use the global [d3.event](Selections#wiki-d3_event). The return value of the event listener is ignored. 

If an event listener was already registered for the same type on the selected element, the existing listener is removed before the new listener is added. To register multiple listeners for the same event type, the type may be followed by an optional namespace, such as "click.foo" and "click.bar".

To remove a listener, pass null as the *listener*. To remove all listeners for a particular event type, pass null as the *listener*, and `.type` as the *type*, e.g. `selection.on(".foo", null)`.

An optional *capture* flag may be specified, which corresponds to the W3C [useCapture flag](http://www.w3.org/TR/DOM-Level-2-Events/events.html#Events-registration): "After initiating capture, all events of the specified type will be dispatched to the registered EventListener before being dispatched to any EventTargets beneath them in the tree. Events which are bubbling upward through the tree will not trigger an EventListener designated to use capture."

If *listener* is not specified, returns the currently-assigned listener for the specified *type*, if any.

<a name="d3_event" href="Selections#wiki-d3_event">#</a> d3.<b>event</b>

Stores the current event, if any. This global is during an event listener callback registered with the [on](Selections#wiki-on) operator. The current event is reset after the listener is notified in a finally block. This allows the listener function to have the same form as other operator functions, being passed the current datum `d` and index `i`.

The `d3.event` object is a [[DOM event|https://developer.mozilla.org/en-US/docs/DOM/event]] and implements the standard event fields like `timeStamp` and `keyCode` as well as methods like `preventDefault()` and `stopPropagation()`. While you can use the native event's [[pageX|https://developer.mozilla.org/en/DOM/event.pageX]] and [[pageY|https://developer.mozilla.org/en/DOM/event.pageY]], it is often more convenient to transform the event position to the local coordinate system of the container that received the event. For example, if you embed an SVG in the normal flow of your page, you may want the event position relative to the top-left corner of the SVG image. If your SVG contains transforms, you might also want to know the position of the event relative to those transforms. Use the [d3.mouse](#wiki-d3_mouse) operator for the standard mouse pointer, and use [d3.touches](#wiki-d3_touches) for multitouch events on iOS.

<a name="d3_mouse" href="#wiki-d3_mouse">#</a> d3.<b>mouse</b>(<i>container</i>)

Returns the *x* and *y* coordinates of the current [d3.event](#wiki-d3_event), relative to the specified *container*. The container may be an HTML or SVG container element, such as an [[svg:g|http://www.w3.org/TR/SVG/struct.html#Groups]] or [[svg:svg|http://www.w3.org/TR/SVG/struct.html#SVGElement]]. The coordinates are returned as a two-element array [*x*, *y*].

<a name="d3_touch" href="#wiki-d3_touch">#</a> d3.<b>touch</b>(<i>container</i>[, <i>touches</i>], <i>identifier</i>)

Returns the *x* and *y* coordinates of the touch with the specified identifier associated with the current [d3.event](#wiki-d3_event), relative to the specified *container*. If *touches* is not specified, defaults to the current event’s [[changedTouches|http://developer.apple.com/library/safari/documentation/UserExperience/Reference/TouchEventClassReference/TouchEvent/TouchEvent.html#//apple_ref/javascript/instp/TouchEvent/changedTouches]]. The container may be an HTML or SVG container element, such as an svg:g or svg:svg. The coordinates are returned as an array of two-element arrays [ [ *x1*, *y1*], [ *x2*, *y2*], … ]. If there is no touch with the specified identifier in *touches*, returns null; this can be useful for ignoring touchmove events where the only some touches have moved.

<a name="d3_touches" href="#wiki-d3_touches">#</a> d3.<b>touches</b>(<i>container</i>[, <i>touches</i>])

Returns the *x* and *y* coordinates of each touch associated with the current [d3.event](#wiki-d3_event), based on the [[touches|http://developer.apple.com/library/safari/documentation/UserExperience/Reference/TouchEventClassReference/TouchEvent/TouchEvent.html#//apple_ref/javascript/instp/TouchEvent/touches]] attribute, relative to the specified *container*. The container may be an HTML or SVG container element, such as an svg:g or svg:svg. The coordinates are returned as an array of two-element arrays [ [ *x1*, *y1*], [ *x2*, *y2*], … ]. If *touches* is specified, returns the positions of the specified touches; if *touches* is not specified, it defaults to the `touches` property on the current event.

<a name="transition" href="Selections#wiki-transition">#</a> selection.<b>transition</b>()

Starts a [[transition|Transitions]] for the current selection. Transitions behave much like selections, except operators animate smoothly over time rather than applying instantaneously.

<a name="interrupt" href="#wiki-interrupt">#</a> selection.<b>interrupt</b>()

Immediately interrupts the current [transition](Transitions), if any. Does not cancel any scheduled transitions that have not yet started. To cancel scheduled transitions as well, simply create a new zero-delay transition after interrupting the current transition:

```js
selection
    .interrupt() // cancel the current transition
    .transition(); // preempt any scheduled transitions
```

### Subselections

Whereas the top-level select methods query the entire document, a selection's [select](Selections#wiki-select) and [selectAll](Selections#wiki-selectAll) operators restrict queries to descendants of each selected element; we call this "subselection". For example, `d3.selectAll("p").select("b")` returns the first bold ("b") elements in every paragraph ("p") element. Subselecting via selectAll groups elements by ancestor. Thus, `d3.selectAll("p").selectAll("b")` groups by paragraph, while `d3.selectAll("p b")` returns a flat selection. Subselecting via select is similar, but preserves groups and propagates data. Grouping plays an important role in the data join, and functional operators may depend on the numeric index of the current element within its group.

<a name="select" href="Selections#wiki-select">#</a> selection.<b>select</b>(<i>selector</i>)

For each element in the current selection, selects the first descendant element that matches the specified *selector* string. If no element matches the specified selector for the current element, the element at the current index will be null in the returned selection; operators (with the exception of [data](Selections#wiki-data)) automatically skip null elements, thereby preserving the index of the existing selection. If the current element has associated data, this data is inherited by the returned subselection, and automatically bound to the newly selected elements. If multiple elements match the selector, only the first matching element in document traversal order will be selected.

The *selector* may also be specified as a function that returns an element, or null if there is no matching element. In this case, the specified *selector* is invoked in the same manner as other operator functions, being passed the current datum `d` and index `i`, with the `this` context as the current DOM element. 

<a name="selectAll" href="Selections#wiki-selectAll">#</a> selection.<b>selectAll</b>(<i>selector</i>)

For each element in the current selection, selects descendant elements that match the specified *selector* string. The returned selection is grouped by the ancestor node in the current selection. If no element matches the specified selector for the current element, the group at the current index will be empty in the returned selection. The subselection does not inherit data from the current selection; however, if the [data](Selections#wiki-data) value is specified as a function, this function will be called with the data `d` of the ancestor node and the group index `i` to determine the data bindings for the subselection.

Grouping by selectAll also affects subsequent entering placeholder nodes. Thus, to specify the parent node when appending entering nodes, use select followed by selectAll:

```javascript
d3.select("body").selectAll("div")
```

You can see the parent node of each group by inspecting the `parentNode` property of each group array, such as `selection[0].parentNode`.

The *selector* may also be specified as a function that returns an array of elements (or a NodeList), or the empty array if there are no matching elements. In this case, the specified *selector* is invoked in the same manner as other operator functions, being passed the current datum `d` and index `i`, with the `this` context as the current DOM element. 

### Control

For advanced usage, D3 has a few additional operators for custom control flow.

<a name="each" href="Selections#wiki-each">#</a> selection.<b>each</b>(<i>function</i>)

Invokes the specified *function* for each element in the current selection, passing in the current datum `d` and index `i`, with the `this` context of the current DOM element. This operator is used internally by nearly every other operator, and can be used to invoke arbitrary code for each selected element. The each operator can be used to process selections recursively, by using `d3.select(this)` within the callback function.

<a name="call" href="Selections#wiki-call">#</a> selection.<b>call</b>(<i>function</i>[, <i>arguments…</i>])

Invokes the specified *function* once, passing in the current selection along with any optional *arguments*. The call operator always returns the current selection, regardless of the return value of the specified function. The call operator is identical to invoking a function by hand; but it makes it easier to use method chaining. For example, say we want to set a number of attributes the same way in a number of different places. So we take the code and wrap it in a reusable function:

```javascript
function foo(selection) {
  selection
      .attr("name1", "value1")
      .attr("name2", "value2");
}
```

Now, we can say this:

```javascript
foo(d3.selectAll("div"))
```

Or equivalently:

```javascript
d3.selectAll("div").call(foo);
```

The `this` context of the called function is also the current selection. This is slightly redundant with the first argument, which we might fix in the future.

<a name="empty" href="Selections#wiki-empty">#</a> selection.<b>empty</b>()

Returns true if the current selection is empty; a selection is empty if it contains no elements or only null elements.

<a name="node" href="Selections#wiki-node">#</a> selection.<b>node</b>()

Returns the first non-null element in the current selection. If the selection is empty, returns null.

<a name="size" href="Selections#wiki-size">#</a> selection.<b>size</b>()

Returns the total number of elements in the current selection.

### Extension

<a name="d3_selection" href="#wiki-d3_selection">#</a> d3.<b>selection</b>()

Returns the root selection, equivalent to `d3.select(document.documentElement)`. This function can also be used to check if an object is a selection: `o instanceof d3.selection`. You can also add new methods to the selection prototype. For example, to add a convenience method for setting the "checked" property of checkboxes, you might say:

```js
d3.selection.prototype.checked = function(value) {
  return arguments.length < 1
      ? this.property("checked")
      : this.property("checked", value);
};
```