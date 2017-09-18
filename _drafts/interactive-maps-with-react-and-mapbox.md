---
layout: post
title: Interactive Map Applications with React and Mapbox GL JS
---
<img style="margin-bottom:15px;" src="{{site.url}}/images/interactive-maps-with-react-and-mapbox/globe-1920.jpg"/>
For the [Bird Observatory Center in Skagen](http://www.skagenfuglestation.dk/) we had the requirement to build interactive map applications for touchscreens. So we' ve decided to use vector rendered maps to achieve smooth and seamless transitions between user interactions like panning and zooming. Because our framework of choice for this project was [React](https://facebook.github.io/react/) we wanted to find a way to create our map applications with React components. This post gives a survey of our solution how to wrap the [Mapbox GL JS](https://github.com/mapbox/mapbox-gl-js) Api with React. We presume some previous knowledge about React, ES6 and Mapbox GL JS.

For hosting vector tiles we use [tileserver-gl](https://github.com/klokantech/tileserver-gl). You can get free free vector tiles for non-commercial use from [OpenMapTiles](https://openmaptiles.com/).

Mapbox GL JS requires a map style to know what to render in which appearance from which sources. A map style generally is a set of sources and layers. It can be edited and exported by [Maputnik](http://maputnik.com/editor/) or [Mapbox Studio](https://www.mapbox.com/mapbox-studio/).
If you want to use self hosted tiles, glyphs or fonts you have to provide the corresponding urls manually.


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
##### MapboxGL requires an HTML element
First we need to render a component with a reference pointing to its DOM element. When componentDidMount() lifecycle method is called we can create the Map object by providing the reference object. Also we have to provide the style object and set the data source.

`componentDidMount:`
```js
  this.props.style.sources.openmaptiles.tiles = [
    `${this.props.tileServer}/data/planet-vector/{z}/{x}/{y}.pbf`
  ]

  this.map = new mapboxgl.Map({
    container: this.mapContainer,
    style: this.props.style
  })
```

`render:`
```xml
  <div ref={ e => { this.mapContainer = e } }>
    ...
  </div>
```

##### A feature component needs access to the map object
To add a feature, the map object must be provided to every child with the help of `React.Children.map` and `React.cloneElement`.

`render:`
```js
  const children = React.Children.map(this.props.children, child =>
    React.cloneElement(child, { map: this.map })
  )

  ...
```

##### We have to wait until Mapbox is ready
We have to prevent rendering the children when the state of the component is not *ready*. We have to wait until the first render callback is called by Mapbox to set the components state to *ready*. This triggers a new *render()* call. Now the child components are allowed to get rendered.

`componentDidMount:`
```js
  this.map.on("render", () => {
    if (!this.state.isReady) {
      this.setState({ isReady: true })
    }
  })
```

`render:`
```xml

  ...

  <div ref={ e => { this.mapContainer = e } } >
    { this.state.isReady && children }
  </div>
```

##### Cleanup: We have to remove the features before we remove the map, else we run into errors.
When the component will be removed from the DOM, *componentWillUnmount()* is called. This method is called top down in the component hierarchy. So the cleanup method of our *Map* component is called before its children. If we remove the map here, we evoke errors because the map features have to be removed first. The trick is to call the map removal in a separate task which is queued and executed after all children are unmounted.

`componentWillUnmount:`
```js
setTimeout(() => this.map.remove())
```

## Feature Components
A map feature also can be realized as a React component. To add a feature like a polyline or a point Mapbox demands a data source (e.g. an array of geographic coordinates or a geojson object). Then a new layer, which defines the appearance of the feature, must be added to the map. This layer must refer to the data source.
To query features and to connect a layer with a data source a unique feature id is required. We can provide all these informations to the feature component by properties. We just need to mount, update and unmount these components correctly. Rendering by react is not necessary here.

##### Map feature lifecycle
`componentDidMount:` - Add source and layer
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

`componentDidUpate:` - e.g. update paint properties
```js
  const { id, map, paint } = this.props

  Object.keys(paint).forEach((key) => {
    map.setPaintProperty(
      `${id}`, key, paint[key]
    )
  })
```

`componentWillUnmount:` - Remove source and layer
```js
  const { id, map } = this.props

  map.removeSource(id)
  map.removeLayer(id)
```

`render:` - Do nothing
```js
  return null
```

# Conclusion
The presented solution works well in our interactive exhibition environment. We just wrapped the components we used while this concept easily allows to extend our little framework with more features and interaction components like zoom controls. This is a lightweight solution to support those features we needed in our application.
There are some open source libraries which wrap Mapbox GL JS. We tried some, but they integrated not so well in our application setup and we didn't want to include further third party dependecies. But if you want to work with one of those librares, you should have a look at [react-mapbox-gl](https://github.com/alex3165/react-mapbox-gl) or [react-map-gl](https://github.com/uber/react-map-gl).
