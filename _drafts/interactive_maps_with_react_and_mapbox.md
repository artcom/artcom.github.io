---
layout: post
title: Interactive Map Applications with React and Mapbox GL JS
---

# Introduction
For the [Bird Observatory Center in Skagen](http://www.skagenfuglestation.dk/) we needed to build  interactive map applications for touchscreens. Because our framework of choice for this project was React we wanted to find a way to create our map applications with React components. This post gives a survey of our solution how to wrap the [Mapbox GL JS](https://github.com/mapbox/mapbox-gl-js) Api with React.

# React Integration
The idea is to render a map as a React component and to render features as child components as follows.

Example:
```xml
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
##### MapboxGL requires an HTML element in which the map will be rendered
First we need to render a component with a reference pointing to its DOM element. When componentDidMount() lifecycle method is called we can create the Map object by providing the reference object.

*componentDidMount()*
```js
  this.map = new mapboxgl.Map({
    container: this.mapContainer
  })
```

*render()*
```js
  <div ref={ e => { this.mapContainer = e } }>
    ...
  </div
```

##### A feature component needs access to the map object, e.g. to add its data source and graphical layer
Provide the map object to every child with help of `React.Children.map` and `React.cloneElement`.

*render()*
```js
  const children = React.Children.map(this.props.children, child =>
    React.cloneElement(child, { map: this.map })
  )

  ...
```

##### We have to wait until Mapbox is ready before we can draw features on the map
We prevent rendering the children when the state of the component is not *ready*. We have to wait until the first render callback is called by Mapbox to set the components state to *ready*. This triggers a new *render()* call. Now the child components are allowed to get rendered.

*componentDidMount()*
```js
  this.map.on("render", () => {
    if (!this.state.isReady) {
      this.setState({ isReady: true })
    }
  })
```

*render()*
```js

  ...

  <div ref={ e => { this.mapContainer = e } } >
    { this.state.isReady && children }
  </div>
```

##### Cleanup: We have to remove the features before we remove the map, else we run into errors.
When the component will be removed from the DOM, *componentWillUnmount()* is called. This method is called top down in the component hierarchy. So the cleanup method of our *Map* component is called before its children. If we remove the map here, we evoke errors because the map features have to be removed first. The trick is to call the map removal in a separate task which is queued and executed after all children are unmounted.

*componentWillUnmount()*
```js
setTimeout(() => this.map.remove())
```

## Feature Components
A map feature also can be realized as a React component. To add a feature like a polyline or a point Mapbox demands a data source (e.g. an array of geographic coordinates or a geojson object). Then a new layer, which defines the appearance of the feature, must be added to the map. This layer must refer to the data source.
To query features and to connect a layer with a data source a unique feature id is required. We can provide all these informations to the feature component by properties. We just need to mount, update and unmount these components correctly. Rendering by react is not necessary here.

##### Map feature lifecycle
`componentDidMount()` - Add source and layer
```js
  const { data, id, map, paint } = this.props

  map.addSource(id, {
    type: "geojson",
    data
  })

  map.addLayer({
    id,
    source: id,
    type: "line",
    paint
  })
```

`componentDidUpate()` - e.g. update paint properties
```js
  const { id, map, paint } = this.props

  Object.keys(paint).forEach((key) => {
    map.setPaintProperty(
      `${id}`, key, paint[key]
    )
  })
```

`componentWillUnmount()` - Remove source and layer
```js
  const { id, map } = this.props

  map.removeSource(id)
  map.removeLayer(id)
```

`render()` - Do nothing
```js
  return null
```
