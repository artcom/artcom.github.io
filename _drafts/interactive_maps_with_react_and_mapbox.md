---
layout: post
title: Interactive Map Applications with React and Mapbox GL JS
---

# Introduction
For the [Bird Observatory Center in Skagen](http://www.skagenfuglestation.dk/) we needed to build  interactive map applications for touchscreens. Because our framework of choice for this project was React we wanted to find a way to create our map applications with React components. This post gives a survey of our solution how to wrap the [Mapbox GL JS](https://github.com/mapbox/mapbox-gl-js) Api with React.

# React Integration
The idea is to render a map as a React component and to render features as child components as follows.

```
Example:

  <Map>
    <PointFeature
      data={ [longitude, latitude] }
      id={ `circle-outer-${id}` }
      paint={ {
        "circle-radius": 5,
        "circle-color": "green",
      } } />
    <PathFeature
      id={ pathId }
      data={ geoJsonData }
      paint={ {
        "line-width": 3,
        "line-color": "red"
      } } />
  </Map>
```

## Map Component
- (1) MapboxGL requires an HTML element in which the map will be rendered
  - render a div component with an innerRef pointing its HTML element to an object
  - when componentDidMount is called after render the HTML element already exists in the DOM
  - so here we can create the mapboxgl map object by providing the HTML element object we referenced in the render method before
  ```
    <MapContainer innerRef={ e => { this.mapContainer = e } }>
      { this.state.isReady && children }
    </MapContainer>
  ```
- (2) We have to wait until mapbox is ready before we can draw feaures on the map
  - in the render method we prevent rendering the children when state is not "ready"
  - we wait until the first render callback is called to set the components state to "ready"
  - this triggers a new render call and the child components are allowed to render
  ```
    this.map.on("render", () => {
      if (!this.state.isReady) {
        this.setState({ isReady: true })
      }
    })
  ```
- (3) A feature component needs access to the map object, e.g. to add its data source and layer
  - add the map object to every child with React.Children.map and React.cloneElement in the render method
  ```
    const children = React.Children.map(this.props.children, child =>
      React.cloneElement(child, { map: this.map })
    )
  ```
- (4) Cleanup problem: We have to remove the features before we remove the map, else we run into errors.
  - With React the cleanup method (componentWillUnmount()) is called before the child elements. If we remove the map here, we evoke errors in the cleanup method of the feature components. The trick is to call it in separate task which is queued and executed after all children are unmounted.
   ```
    setTimeout(() => this.map.remove())
   ```

## Feature Components
  A map feature also can be realized as a React component. To add a feature like a polyline or a point amongst others, Mapbox demands a data source like an array of geographic coordinates or a geojson object. Then a new layer must be added to the map which refers to the data source and defines the visual appearance of the feature. All these informatios we can provide via properties to the stateless component which wraps the mapbox api calls. We just need to mount, update and unmount these components. Rendering by react is not necessary here.

  - Map feature lifecycle
    - componentDidMount()
      - add source and layer
    - componentDidUpate()
      - update paint properties
    - componentWillUnmount()
      - remove source and layer
