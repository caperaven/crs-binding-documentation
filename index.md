# Introduction
This documentation is about crs-binding.  
crs-binding is a binding engine developed as a zero dependency standalone library in a world of custom web components. Vanilla web components is a big part of the web today but the one thing that holds it back is the lack of binding.

## Binding engines
There may be a lot of frameworks out there and each has it's own interpretation of binding. There are also some older well established binding engines out there that use handlebar syntax. crs-binding does not try and compete with these but instead provide features as required by it's developer.

Q: What is a binding engine?  
A: There are two parts to web development.

1. View - HTML / CSS representation of the interface.
1. Model / data - JavaScript behind the view managing data and features.

One of the biggest problems you have with web development is getting your data on the screen at the required place and then getting changes back. Binding engines serve to reduce this effort. This is a very simplistic view on what binding engines do.

## Documentation
The documentation found here will include subjects like:

1. Working with observed models, events and expressions.
1. Binding expressions
1. Utility functions
1. Bindable element for custom web components
1. View base acting as the view part of MVVM