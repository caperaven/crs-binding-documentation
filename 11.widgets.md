# Widgets

## Introduction
In this context a widget is a HTML Element that acts as a empty container for dynamic content.  
It does not have it's own context, instead the binding context and html to render must be passed to the widget.

## Using
```html
<crs-widget id="my-widget"></crs-widget>
```

Place the widget anywhere where you need it for your UI.  
In this case we gave it an id attribute to make it easier to target.

The widget is initialized when the context and content is passed to it using the messaging system.

```js
crsbinding.events.emitter.postMessage("#my-widget", {
    context: this,
    html: "<h2>${title}</h2><p>Some paragraph stuff that is not bound</p>"
})
```

The above is called from either a bindable element, view model or other widget.  
The widget shares the context of the item defined as context and thus can bind to the same data as the context.

## Extending

You can extend the widget component and create your own with additional features by extending the class

```
crsbinding.classes.Widget
```

## Conclusiong

A widget is a context sharing, dynamic component that is driven by the eventing system.