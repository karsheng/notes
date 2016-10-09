# Google Maps API
Browser API key: AIzaSyDuoB62jzCkrFV9NVlNHj5cZTvXvjJAdbk
Server API key: AIzaSyCk4iBJ0Y0yHyisw2X9jV4jopucSeQlVB4

## Getting Started with the APIs
```html
<script async defer
  src="https://maps.googleapis.com/maps/api/js?key=MYAPIKEY&v=3&callback=initMap">
</script>
```
1. `async defer` to load the API asynchronously while the rest of the page loads.
1. Callback function will execute when the API is done loading. You can use the function immediately or call it when you want to use it.
1. `maps.googleapis.com` is the endpoint that we're loading the JavaScript from.
1. `v=3` indicates the version 3 of the API.
1. Full reference [guide](https://developers.google.com/maps/documentation/javascript/reference) for the JavaScript API.
1. Get coordinates/geocode [here](https://google-developers.appspot.com/maps/documentation/utils/geocoder/)
1. You can change **features** of google maps, such as _water, land, roads (e.g. highways and local roads), points of interest and all the labels associated with them_.
1. Google Maps documentation on [map styling](https://developers.google.com/maps/documentation/javascript/styling).
1. [MapTypeStyleFeatureType](https://developers.google.com/maps/documentation/javascript/reference#MapTypeStyleFeatureType)
1. Udacity Map Styling Example [Repository](https://github.com/udacity/ud864)
1. What makes up a StreetView Panaroma:
  1. `pov` (point of view) - `heading` (east or west) and `pitch` (up or down)
### Google Maps StreetView
1. Udacity static maps and StreetView imagery example [repository](https://github.com/udacity/ud864/blob/master/Project_Code_6_StaticMapsAndStreetViewImagery.html)
1. Use `radius` to cover a range rather than having it load exact location
1. Google maps styling [documentation](https://developers.google.com/maps/documentation/javascript/styling#creating_a_styledmaptype)
1. Static map parameters: `center`, `zoom`, `key`, `size`
1.
**Libraries** that Google Maps APIs offer: Geometry, Visualization, Places and Drawing.

## Understanding API Services
1. Geocoding, among other Google location services, can be used on the client side and the server side.
1. Server side - no need for user data input, data returns usually in JSON or XML format.
1. Client side - when user input is required for further processing.
1. **Geocoding** - converting addresses to lat lng, **Reverse Geocoding** does the reverse.
1. Geocoding API [documentation](https://developers.google.com/maps/documentation/geocoding/intro#Types)
1. Elevation API [documentation](https://developers.google.com/maps/documentation/elevation/intro)
1. Distance Matrix API [documentation](https://developers.google.com/maps/documentation/distance-matrix/)
1. Directions API [documentation](https://developers.google.com/maps/documentation/directions/)

###Distance Matrix and Directions Specifics
Distance Matrix API [requests](https://developers.google.com/maps/documentation/distance-matrix/intro#RequestParameters) are made with a combination of ORIGIN and DESTINATION values, as well as a TRAVELMODE. Here are some watchpoints when making requests:

Regardless of the travel mode chosen, you can pass in up to 25 origin or 25 destinations per request, but ONLY up to 100 elements and elements = origins*destinations. For example, 10 origins and 10 destinations = 100 elements. This value is increased to up to 625 elements for Premium Plan customers, when using the Web Service.
While most other services are Rate Limited (queries per second can’t exceed 50), the Distance Matrix API is [rate limited](https://developers.google.com/maps/documentation/javascript/distancematrix#quotas) in terms of ELEMENTS (not queries). If too many elements are requested within a certain time period, an OVER_QUERY_LIMIT response code will be returned.
Some parameters only are valid for certain travel modes - for example:
AvoidTolls and AvoidHighways are ignored if the travel mode = transit
All of the TransitOptions are only valid if the travel mode = transit
departureTime can be passed as part of the DrivingOptions or TransitOptions parameters

Directions API [requests](https://developers.google.com/maps/documentation/javascript/directions#DirectionsRequests) also involve ORIGIN, DESTINATION and TRAVELMODE but have the optional parameters of WAYPOINTS or VIA points as well. Here are some watchpoints when making requests:

For the client-side Directions service (using the Javascript API), up to 8 waypoints per request can be specified, in addition to the origin and destination, making a total of 10 geocoded points per request. This limit increases from 8 to 23 for Premium Plan customers
For the web service, up to 23 waypoints per request can be specified as long as you provide a valid identifier, such as your API key. Your [response code](https://developers.google.com/maps/documentation/directions/intro#DirectionsResponseElements) will indicate if this is exceeded.
Waypoints are not supported for the TRANSIT travel mode

1. [Roads API](https://developers.google.com/maps/documentation/roads/intro) is useful to avoid jittery GPS and make accurate predictions of the route a subject is taking. This will be useful for delivery services.
1. **Interpolating snapToRoads requests** - when you pass up to 100 lat/lng points (not addresses!) into a roads snapToRoads request, you can choose to turn those 100 into many more points, to give you a fuller and clearer picture of the road you travelled on. However, if you have very few, very widespread points, interpolation may not give you an accurate picture - the more data - the better.  
1. [Place Autocomplete API](https://developers.google.com/maps/documentation/javascript/examples/places-autocomplete)
1. [Place Details API](https://developers.google.com/places/place-id)
**Nearby Search**
Formerly know as simply Place Search, the nearby search is the simplest way to find places within a specified area. You are able to specify a center, and a radius to search within. This search will return a list of 20 places by default, and 60 with a Premium Plan license.

**Text Search**
The Text search allows a user or system to execute a places search without a specified location, by using a text query. The search can also be biased by passing location information.

**Radar Search**
Radar Search allows the user to specify the same parameters as the Nearby Search, and will return 200 results instead of 20, but with limited returned data.

For all the searches, you can pass the value of the next_page_token to the pagetoken parameter of a new search to see the next set of results. If the next_page_token is null, or is not returned, then there are no further results.

The following is a list of the required and optional parameters, and which search methods are able to be used with them.
![Places API Usage](img/placesAPIUsage.png)
1. UTC stands for Coordinated Universal Time
1. (Google Maps Timezone API)[https://developers.google.com/maps/documentation/timezone/intro]
1. 
