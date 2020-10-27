# Dynamic popup content

## Introduction

This is a design strategy for a custom web component allowing for dynamic content with as little code possible.
To make this work we will need to do the following:

1. Create a custom component that acts as the vehicle for the dynamic content.
1. Display and hide the component using event aggregation expressions on buttons.
1. Implement a convention so that we know what content to load.

To make this work our component will need to make use of the `crs-widgets` component.

## Files

We will need a folder called templates where all our template HTML files will be in.
For each supported template create a html file for that template in this folder.

## Template

Consider the following template HTML example

```html
<div>
    <h2 data-drag="true">${titles.svgProperties}</h2>
    <svg role="button" id="btnClose" width="24" height="24"><use xlink:href="#close"></use></svg>
</div>

<div class="properties">
    <label>
        <div>Width</div>
        <input type="number" value.bind="svg.width" />
    </label>

    <label>
        <div>Height</div>
        <input type="number" value.bind="svg.height" />
    </label>

    <label>
        <div>Background</div>
        <input type="color" value.bind="svg.background" />
    </label>
</div>
```

There are some conventions to take note of.

1. Mark the element that enables dragging of component with `data-drag="true"`.
1. Mark the element that will close this dialog with the id attribute of `btnClose`.

Note that the binding expressions assumes a known binding context.  
You need to send on the right context id for this template to function properly.

## Component internals

I am not going to deal with the drag and drop functionality here, it's simple enough not to include it here.  
What I do want to focus on is how the load the template and allow the binding to work.

### Required HTML
```html
<crs-widget></crs-widget>
```

### Required JS

```js
async onMessage(args) {
    if (args.key == "close") {
        return await this.close();
    }

    const template = await fetch(`./templates/${args.key}-template.html`).then(result => result.text());

    crsbinding.events.emitter.postMessage(`#${this.id} crs-widget`, {
        context: args.context,
        html: template
    });    
}
```

The `onMessage` function is a standard feature in crs-binding for <a target="_blank" href="https://github.com/caperaven/crs-binding-documentation/blob/master/3.%20binding-expressions.md#postmessage">targeted messaging</a>.  

## Using the above

Let us say the tag for this element is `popup-container`.

```html
<popup-container hidden></popup-container>
```

We want a svg button that when we click it will show some template in that component.

```html
<svg click.post="shapes['popup-container'](context=${_dataId})" role="button" tabindex="0" width="32" height="32"><use xlink:href="#shapes" title="shapes"></use></svg>
```

In the above example we are passing the following information to the component via the `onMessage` function of that component.

1. key = "shapes"
1. context = the parent's context id <number>

When the onMessage fies, it fetches a template by the name of `shapes-template.html` from the templates folder.  
It then sends this information to it's `crs-widget` component via event aggregation who in turn will render the content and take care of the binding.

## Conclusion

Using this strategy you can use this component to render any content at any time either using crs-binding's event aggregation expressions or javascript functions.
Because you can load any template at any time by reusing this component, the UI is lightweight and loads the content only when you need it.  
This provides for better memory usage.  
When you "close" the component you discard it's content and make it invisible again for the next batch when and if you need it.