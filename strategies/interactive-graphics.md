# Interactive graphics

## Introduction

This document lays out a strategy on how to implement interactive graphics using crs-binding.
There are a handful of parts to take note of.

1. Separation of concern
1. Responding to property changes
1. Posting messages using pubsub
1. Custom elements for UI parts
1. ES6 classes for managers
1. Object literals for lightweight group convention based functions

## Diagram

![diagram](https://github.com/caperaven/crs-binding-documentation/blob/master/strategies/images/interactive-graphics.jpg)

## Areas of concern

1. View Model
1. Marker custom element
1. Color palette element
1. Render manager
1. Different renderers

### View Model

The view model contains all the context data.   
The view model context is passed too components using binding expressions on `data-context` attributes.  
The view model acts as the glue between the different parts and manages the state property.

The state property drives most of the interactions.  
Everything responds to state changes on the view model.  
Each feature that is state aware listens to the state changes using: 
`crsbinding.events.listenOnPath(Number(this.dataset.context), "state", this.stateChangedHandler);`  
On state change, the feature enables and disables input events that allow the function to operate.

## Features

A feature in this context means either a component or class.  
Features read and write to the binding data using the contextId passed to it from the view model.  
Features only communicate in one of two ways.

1. Events
1. Writing property values to the binding context

Each feature is only aware of itself.  
Additional information that the feature needs is accessed through:

1. Binding context data
1. Attributes (if a custom element), as far as possible use `data-` attributes.
1. Constructor (if a class)

These are common states, but you can add more if required.

1. None - do nothing but object picking through click events.
1. Select - draw a selection rect that will set the selected items to all items that fit in that space.
1. DrawBox - this state indicates that we are performing draw operations and require the marker to draw a box shaped marker.
1. DrawLine - we are still drawing, but we require the marker to draw a line shaped marker. 

Enable and disable events as you need it.  
A example of this is to register mouse move and mouse up events once the mouse down event has fired.  
These events are removed again on mouse up.  
Each feature is responsible for managing it's own events.  
If you are going to have different features using the same event management, consider using a base class.

## Marker

The marker is a custom component and is always present.
It has only one concern and that is to draw a selection shape when the state allows for it.

When drawing things like rectangles or boxed shaped items the marker needs to draw a box shape.  
Typically, this box shape has no fill color but a dashed border pattern.  
When drawing things like circles, lines or paths, the boxed selection marker does not make sense.  
In those cases you want to draw a line selection marker.

The marker component does not set state but only responds to state.  
Events it requires for operation are enabled or disabled based on state.  
For example, mousedown event on canvas is only present when we are doing selection or drawing.

The marker posts two events during operation.

1. "start-selection"    - fired when selection starts (mouse down)
1. "update-selection"   - fired when selection changes (mouse move).

The information you send depends on the selection type.  

1. box selection - send x, y, width, height
1. line selection - send x, y, x2, y2

Events are published using <a href="https://github.com/caperaven/crs-binding-documentation/blob/master/7.event-aggregation.md">crsbinding.events.emitter.emit</a>

## Renderer

The renderer is a thin shell that manages the active render object.  
It uses a property to set the active renderer based on the binding data on what it is rendering.  
This can be either a custom element, or a class, either way it needs the following information.

1. convention on what is being drawn.
1. the name of the svg to draw in.

The active render object does the actual object creation and manipulation.
When the shape is "rectangle" we need to set the active renderer to the "rectangleRenderer".  
If the shape is a "circle" the active renderer should be the "circleRenderer".  
One of the easies ways to manage this is through a map where the key is the shape name and it returns you the renderer you need.  
This way you can also easily register new shapes to the renderer through an "add" function that sets the renderer on the map.  
This also allows you to easily override the default renderer if you need to.

A active render object will need at least these two functions.

1. create   - create the new shape and add it to the target canvas
1. update   - update the dimensions of the shape based on the information provided

Create is called when the marker fires the "start-selection" event.  
Update is called when the marker fires the "update-selection" event.
