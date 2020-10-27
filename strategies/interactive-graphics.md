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

