# Torex documentation

Torex package with installation guides can be found [here](https://github.com/nicoth-in/torex).

## Hello, Torex

The core parts of the Torex framework are elements. You can use them from Torex scope.

```JS
const { Div, Span } = Torex;
```

If you are using npm package, you need to import Torex as a es6 module.

```JS
import Torex from '@torexjs/torex';
const { Div, Span } = Torex;
```

Now create div element by calling `Div` class with options.

```JS
let element = new Div({/* Options */});
console.log(element);
```

Output would be

```HTML
<div></div>
```

## Adding attributes

In most cases you will need to create elements with some attributes.
This can be done in constructor:

```JS
let element = new Div({
  attr: {
    id: "my-div",
    class: "row",
  }
});
```

```HTML
<div id="my-div" class="row"></div>
```

Or you can attach attributes after construction.

```JS
let element = new Div({ });

element.setAttribute("id", "my-div");
element.setAttribute("class", "row");

```

Output would be the same.

## Children

Option `items` accept Element or array of child Elements.

```JS
let element = new Div({
  items: [
    new Span({ items: new Text("First row") }),
    new Span({ items: new Text("Second row") }),
  ]
});
```

```HTML
<div>
  <span>First row</span>
  <span>Second row</span>
</div>
```

Standard HTML method also works.

```JS
let element = new Div({ });

element.appendChild(new Span({ items: new Text("First row") }));
element.appendChild(new Span({ items: new Text("Second row") }));

```

## Using Classes

All Torex elements may be extend.

```JS
class MyOwnDiv extends Div {
  constructor() {
    super({ items: new Text("Click me!") }); // Always use super
    this.myMethod = this.myMethod.bind(this); // Bind current scope to your method
    this.addEventListener("click", this.myMethod); // Add 'click' event
  }
  myMethod(ev) {
    this.mySuperFunction();
  }
  mySuperFunction() {
    console.log("Clicked");
  }
}

let element = new MyOwnDiv({ });
```

You should see "Clicked" in console after clicking this element.

```HTML
<div>Click me!</div>
```

## Customizing

If you set `custom` field while creating element, you will be able to customize HTML elements in your document.
This will be very helpful on server generated pages.

For example, you have this HTML:

```HTML
<button is="my-button"></button>
```

And JS

```JS
class MyButton extends Button {
  constructor() {
    super({ custom: "my-button" });

    this.onClick = this.onClick.bind(this);
    this.addEventListener("click", this.onClick);
  }
  onClick(ev) {
    console.log("Clicked");
  }
}
```

We need to call our class to initialize button.

```JS
new MyButton();
```

And now, you should see "Clicked" in console after clicking button.

### Autonomous elements

With Torex you can build custom elements without extending HTML element.

```JS
class MyNewElement extends Torex.Autonomous {
  constructor() {
    super({ custom: "my-new-element", items: new Text("Text inside") });
  }
}
document.body.appendChild(new MyNewElement());
```

Output would be:

```HTML
<my-new-element>Text inside</my-new-element>
```

You *must* set custom property in Autonomous elements.

## Empty (Fragment) elements

You can create an empty Fragment element without any tag. It can hold items and after attaching to the DOM tree disappears. (See Document Fragment for more)
```JS
class EmptyContainer extends Torex.Fragment {
  constructor() {
    super({items: new Text("Some text") });
  }
}
```
```HTML
Some text
```

## Shared storage

Shared storage is a build-in class attached to every Torex element.
It is basically storage with `set()` and `get()` methods, but the data it stores is shared with children.
So, if you set/get some data on children it will be actually set/get on the root parent.
Shared storage can be accessed via `sharedStorage` field on Torex elements.

```JS

  let span = new Span({ });
  let element = new Div({ items: span });

  element.sharedStorage.set("key", "value");
  span.sharedStorage.get("key"); // Will return 'value'

```

Method `clear()` will clear all data in storage.

Shared storage also dispatches "sharedstoragechanged" event on elements when being changed.
