---
layout: post
title:  "Framer Modules - TemplateScrollComponent"
date:   2017-09-27
categories: framer
---

Framer is an incredibly flexible software prototyping tool fundamentally built upon a JavaScript library, framer.js. While you don’t need to be proficient in JS or HTML/CSS, it certainly helps and is what sets Framer apart from other popular prototyping tools on the market. (Framer uses [CoffeeScript](http://coffeescript.org/) which is a simpler, clean language built upon JavaScript).

As a product manager, and someone who dabbles in JS, I’ve been eyeballing Framer and yearning for an opportunity to kick the tires. I’ve not been disappointed. 

It’s no surprise that many of my early prototypes aren’t exactly useful and look a lot like other prototypes on the web. I started simply by exploring what others have created to understand what can and can’t be done (easily.)

My favorite prototype so far :)

![Sticky headers!](https://github.com/jonmmay/Framer-experiments/blob/master/sticky-header.framer/sticky-header.gif?raw=true)

<p class='caption' markdown='1'>
[Check out the prototype](https://framer.cloud/OUTsL/ "It's so fun!")
</p>
It’s in this exploration, and increased understanding of the tool, that I’ve come to create my own modules. My very first module was designed to extend Layers to support common iOS touch triggers like “touchUpInside” and “touchDown”. I have to dig this up again and verify it behaves as expected but what I’d like to focus on here is my latest module.

I designed this module to iterate over a list (or array) of data and quickly stamp out templated layers within a ScrollComponent. 

If you primarily rely upon Sketch to mockup your designs, it’s likely you already have a solution in InVision’s Craft plugin. And you probably also know that what you export from Sketch is a static image; great for quick prototypes but limiting if you want to create high fidelity prototypes leveraging dynamic data.

With the introduction of Framer’s Design view, I saw an opportunity to produce dynamic lists in scroll components with the majority of the design work remaining in the Design view, not in code.

Continue on to see how it works or [check it out](https://framer.cloud/fqUtD/ "Framer prototype").

In Design view, draw a layer you wish to be your scroll component. Within this layer, draw another layer that will be your template layer.

![Drawing in Framer]({{ site.url }}/assets/2017-09-27-framer-modules-template-scrollcomponent/create_layer.gif)

Make sure that you’ve created targets for both the scroll component layer and template layer. We need these targets in code view.

![Targeting layers]({{ site.url }}/assets/2017-09-27-framer-modules-template-scrollcomponent/target_layer.gif)

Toggle over to Code view.

You should see everything you’ve created in Design view as well as a layer hierarchy list with your two layers. We’ll wrap the scroll component layer to turn it into a real ScrollComponent, but, we’ll use `TemplateScrollComponent`, a derivative of ScrollComponent.

```coffeescript
scroll = new TemplateScrollComponent
    wrap: scrollView
```

<p class='caption' markdown='1'>
psst. You can find this module on Github, [here](https://github.com/jonmmay/TemplateScrollComponent).
</p>

Now that you have a working scroll component you can define some properties to build your content. This component requires an integer value for the `numberOfItems` property and a reference to your template layer the `templateItem` property. You can also provide a `gutter` property to display padding between layers.

```coffeescript
scroll.props =
    scrollHorizontal: false
    numberOfItems: 20
    templateItem: Template_cell
    gutter: 20
```

The last requirement to build your content is to define the function that will be called for every item, `forItemAtIndex`. This function will pass the index of the item as well as the template layer and expects a copy of the template layer to be returned.

```coffeescript
scroll.forItemAtIndex = ( index, layer ) ->
    # Copy Template_cell layer
    cell = layer.copy()
    # Do something here to modify cell layer
    title = cell.childrenWithName( "Title" )[ 0 ]
    title.text = index
return cell
```

![Listing indexes]({{ site.url }}/assets/2017-09-27-framer-modules-template-scrollcomponent/list_indexes.gif)

Connect this to an array and you’ve got something exciting!

```coffeescript
data = [
    {title: "Hennessey Venom GT"},
    {title: "Bugatti Chiron"},
    {title: "Bugatti Veyron Super Sport"},
    {title: "SSC Ultimate Aero"}
]
scroll.numberOfItems = data.length

scroll.forItemAtIndex = ( index, layer ) ->
    # Copy Template_cell layer
    cell = layer.copy()
    # Do something here to modify cell
    title = cell.childrenWithName( "Title" )[ 0 ]
    title.text = data[ index ]
return cell
```

Now a bonus for TextLayers within the template layer!

`TemplateScrollComponent` has a helper function to apply data to layer text using the format “{dataProperty}” where “dataProperty” is a property of an object within your array data. Now you can get rid of all the nasty code that searches for children layers by a certain name.

```coffeescript
scroll.forItemAtIndex = ( index, layer ) ->
    # Copy Template_cell layer
    cell = layer.copy()
    TemplateScrollComponent.applyTemplate cell, data[ index ], dataFormatter    
    return cell
```

`TemplateScrollComponent.applyTemplate` is build upon the TextLayer’s `template` and `templateFormatter` properties. This function expects two parameters with an optional third: template layer, data, and data formatter.


Last Bonus! This module is also available through [Framer Modules](https://github.com/kysely/framer-modules#docs) which makes discovering and installing modules easier than ever.

This wouldn’t be complete without caveats:

* At this time the module scrolls vertically
* This is specific to scroll components (sorry page components)

That said, I plan to continue to iterate upon this module so that it might help others make more engaging prototypes.
