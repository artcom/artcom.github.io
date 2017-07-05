---
layout: post
title: Interactive Map Applications with React and Mapbox GL JS
---

# Introduction
- BOCS
- Interactive Map Applications
- Offline Support
- Vector and Raster Tiles

# React Integration
- idea is to render a map as React component and render features as child components

# Map Component
- first problem is, mapboxgl requires an HTML element in which the map will be rendered
  - render a div component with an innerRef pointing its HTML element to an object
  - when componentDidMount is called after render the HTML element already exists in the DOM
  - so here we can create the mapboxgl map object by providing the HTML element object we referenced in the render method before
- second, we have to wait until mapbox is ready before we can draw feaures on the map
  - in the render method we prevent rendering the children when state is not "ready"
  - we wait until the first render callback is called to set the components state to "ready"
  - this triggers a new render call and the child components are allowed to render
- third, a feature component needs access to the map object, e.g. to add its data source and layer
  - add the map object to every child with React.Children.map and React.cloneElement in the render method

- Cleanup problem: setTimeout(() => this.map.remove())

# Feature Components
  - Map feature lifecycle
    - componentDidMount()
    - componentDidUpate()
    - componentWillUnmount()
    - render()
  - Components:
    - Marker
    - Overlays
    - Path
    - Point
    - Polygon
    - GeoJSON

# Hosting map data
  - Tileserver

# Style
  - Create style
  - Edit sources

# Fonts and Sprites

# Problems
