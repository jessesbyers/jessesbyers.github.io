---
layout: post
title:      "Watershed Monitor: JavaScript and Rails Project"
date:       2020-03-12 10:57:53 -0400
permalink:  watershed_monitor_javascript_and_rails_project
---


*I created Watershed Monitor to fill a real environmental need. We currently have the science we need to make the needed changes to protect our water quality, and we have many laws and regulations at every level related to managing and protecting our quality. However, most government agencies and organizations lack the capacity to effectively monitor and enforce these regulations and support best practices. This application aims to help reduce this capacity problem. By calling on the public to collect and report data on water quality, the data can be used to help agencies and organizations prioritize their enforcement and support where it is most needed.*

![Imgur](https://i.imgur.com/FpAMQBn.png)

[Check out the project on Github](https://github.com/jessesbyers/watershed-monitor) and [Watch a video walkthrough](https://drive.google.com/file/d/1LQ7Do3U6YBOZkHgLxWyPjXvE9GQQYANr/view?usp=sharing).

## Project Requirements
This project required me to create a Single Page Application with a Rails API Backend and JavaScript Frontend. All communication between the frontend and backend was required to happen asynchronously through AJAX with data communicated in JSON format. It needed to organize data through JavaScript Objects and Classes, include a has many relationship, and  include at least 3 AJAX calls using fetch to complete CRUD actions. I fulfilled these requirements by integrating the Google Maps Javascript API so that users could use an interactive map interface in order to more easily input geographic data and view data without having to worry about latitude and longitude coordinates.

### Rails API Backend
The Rails component of this project is very straightforward. There is a Category model and an Observation model, and each Category has many Observations, and each Observations belongs to a Category. The Category model allows for easy organization and filtering of the data by category, and users primarily interact with the Observation model.

```
class ObservationsController < ApplicationController

    def new 
        observation = Observation.new
    end

    def create 
        observation = Observation.new(observation_params)
        observation.save
        render json: ObservationSerializer.new(observation)
    end


    def index
        observations = Observation.all 
        render json: ObservationSerializer.new(observations)
    end

    def show
        observation = Observation.find(params[:id])
        render json: ObservationSerializer.new(observation)
    end

    def destroy 
        observation = Observation.find(params[:id])
        observation.destroy
    end

    private

    def observation_params
        params.require(:observation).permit(:name, :description, :category_id, :latitude, :longitude)
    end

end
```

The Observations Controller includes logic for the create, read, and destroy actions, and leverages functionality from the fast JSON API gem to create serializers and customize how data is organized for communication with the JavaScript front end.
```
class ObservationSerializer
  include FastJsonapi::ObjectSerializer
  attributes :name, :description, :latitude, :longitude, :category_id, :created_at, :category
end
```

As a result, observation index data is displayed with associated categories at localhost:3000/observations:
![Imgur](https://i.imgur.com/QFBXVGY.png)
 
### Google Maps JavaScript API Integration
This application relies heavily on the Google Maps Javascript API for the frontend display and user interaction. This API is a codebase that includes JavaScript functions and objects such as maps, markers, and info windows. The first step in getting the front end up and running was to research and experiment with how these objects can be created, modified, and deleted. The[ documentation](https://developers.google.com/maps/documentation/javascript/tutorial) was very helpful in navigating this exploration.

To integrate the maps API, I needed to add a script to the bottom of the body of my index.html file. This script made a connection to the google maps API, included my access key, and included a callback to the initMap() function which would set up my base map.
```
    <script id="api" async defer src="https://maps.googleapis.com/maps/api/js?key=##############I&callback=initMap"
    type="text/javascript"></script>
```

Each type of object has a constructor function which allows construction of new instances of each object with a variety of options, such as the examples below. 
#### Setting up the base map
```
    let mapCenter =  { lat: 45, lng: -90} 
    let map = new google.maps.Map(document.getElementById('map'), {zoom: 3, center: mapCenter});
```
This creates a map centered on North America, with a zoom level allowing us to view the entire continent.

#### Constructors for Maps, Markers, and Info Windows

```
        let obsMarker = new google.maps.Marker({
                position: {lat: this.latitude, lng: this.longitude},
                map: map,
                label: {
                    text: number.call(this),
                    fontSize: "8px"
                },
                icon: iconColor.call(this)
        })
```
This creates a new marker object based on geographic coordinates from the database, and it can be customized for icon shape, color, label text, size, etc.

```
        let infowindow = new google.maps.InfoWindow({
        content: observationDetails
        });
```
This creates a new info window, that can be populated with details fetched from the database.

#### Setter and Getter Methods
Beyond these constructors, I also used google's built-in setter and getter methods to obtain and change coordinates, to set or reset markers on a map, and to change specific properties of the markers on the map. For example:
```
   function placeMarker(latLng, map) {
        let placeholder = new google.maps.Marker({
        position: latLng,
        map: map
        });
        placeholder.setDraggable(true)
        placeholder.setIcon('http://maps.google.com/mapfiles/ms/icons/blue-pushpin.png')

        let markerCoordinates = [placeholder.getPosition().lat(), placeholder.getPosition().lng()]

        newMarkerArray.push(placeholder)

        this.showNewObservationForm(markerCoordinates, map, placeholder)
    }
```
Within this function, the setDraggable() setter method is used to make the marker draggable when creating a new observation for the map, and uses the setIcon() method to change the marker icon from the default shape to a pushpin shape. The getPosition() getter method is used to then collect the exact latitude and longitude coordinates from the pushpin placeholder marker, so they can be stored in an array and later used in the post request to the backend while creating a new observation entry in the database.

#### Event Listeners and Events
Finally, the goole maps API includes many event listeners and events that are similar to vanilla JavaScript events. Since many users are accustomed to using clicks, double clicks, and drags to navigate a map on any site, I needed to carefully plan out how to enable and disable event listeners so that my custom events for adding and deleting database entries were not conflicting with regular google map navigation events.
```
    addObs.addEventListener('click', function() { 
        addObs.disabled = true
        alert("Click on a location on the map to add a new observation.");
        // Event listener to place marker on map with click on location
        let addMarkerListener = map.addListener('click', function(e) {
            Observation.placeMarker(e.latLng, map);
            // Remove event listener so only one marker can be added at a time
            google.maps.event.removeListener(addMarkerListener)
        });
    })
```
This example shows how I paired a traditional event listener (clicking on the "Add" navbar button) with a google map listener in order to allow users to add a marker to the map as well as add the data to the database. At the end of the function, the event listener is removed to re-enable the default google maps behavior.


### Object Oriented Javascript Frontend


### Communication Between the Frontend and Backend

## Future Enhancements
While this project has succeeded in delivering the functionality needed for the public to report water quality observations, more work needs to be done to make it a fully-functioning application. In the future, I would like to add the following features:
* Add user login, and allow users to view all data, but only delete their own observations
* Add an admin role, which allows a government entity or organization to access the database directly and work with the data in more complex ways than the public would.
* Replace the Water Quality Data category with a new class for Water Quality, and fetch quantitative data from a public API to display on the map instead of user input.


If you didn't already, feel free to [check out the project on Github](https://github.com/jessesbyers/watershed-monitor) and [watch a video walkthrough](https://drive.google.com/file/d/1LQ7Do3U6YBOZkHgLxWyPjXvE9GQQYANr/view?usp=sharing).

