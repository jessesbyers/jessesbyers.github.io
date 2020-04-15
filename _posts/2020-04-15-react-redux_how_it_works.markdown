---
layout: post
title:      "React-Redux: How It Works"
date:       2020-04-15 23:38:05 +0000
permalink:  react-redux_how_it_works
---


*I've completed my project, added comments within my code, written a technical blog post, and recorded my video walkthrough. Now, it's review time. In this blog post, I'll review the core concepts of React and Redux by stepping through my project files and explaining what is happening under the hood. This should be a great way to solidify my own understanding, and I hope that it helps you, too.*

Before you read on, take a quick peek at my last blog post describing my project, the [Climate Data Dashboard](https://jessesbyers.github.io/climate_data_dashboard_react-redux_project), which gives an overview of the project and describes my greatest challenge and lesson learned.

## React
### What is React?
React is a JavaScript library for building user interfaces. It works by rendering static and dynamic components in a virtual DOM which is compared against the actual DOM in the browser in real time. When react detects a change between the previous state (or what is currently rendered in the browser view) and its state in the virtual DOM, it can quickly "react" by re-rendering that DOM element. In order to do this, a react application is built out of a collection of individual component elements that render JSX, is translated into pure HTML elements for rendering on the DOM. 

### File Structure
At the core of a react app is the public folder and an src folder. The public folder contains the** index.html** file, which in turn loads the entire application. The file is a simple template with a head section and a main div element with an id of "root". My file also includes some scripts to support react-bootstrap, which I have used for styling the application.

The src folder contains all of the other main application files, including container, component, action, and reducer files (all discussed below). First, the **index.js** file imports a number of packages and functions that will help set up the application within the DOM and organize how data will be managed throughout the app. React and React-DOM allow us to write JSX in our files and enable that JSX to be renedered as html in the DOM, using the createElement method to translate between the languages. The redux imports allow us to create a top-level state that any component can connect to, which makes data management more efficient throughout the app. The Thunk import allows us to use middleware to manage our asynchronous actions, and ensure that the program will wait for fetches to the API to be completed before moving on to the next line of code.

Essentially, the main function of this index.js file is to set up the "store" that will store and update the data for the application, and render the parent component of the app onto the DOM. We use the createStore function to create a store that references our rootReducer as an argument as well as the thunk middleware. It then renders the App component on the DOM at div#root, on the index.html file. This App component is wrapped in the provider, which passes down the data store as props, which will make the data accessible to any child of the app component that requests access to it.

### Containers
Next, we have the containers and components folders. These two files contain all of the components, or elements that can be rendered on the DOM. In general, all of these components can take in data from a local state, from props, or from the store, and interpolate that data into the JSX code to render dynamic content. There are different types of components that do this in different ways.

### Routing
My app has four main containers. **App.js** is the parent component that houses all of the other child components in the app. App.js is responsible for all of the routing that happens within the app. It imports the BrowserRouter component from react, and wraps all of the other components within it. The **NavBar** component is a static component rendered on every view, so it is not passed any props. The next four routes are defined with a path as well as a component, and this directs which components will be rendered at a particular time based on the url in the browser's address bar. These paths can be static, such as the paths for the **Home** and **DataSourceInput** components, but can also be dynamic and include nested routes, as in the **New** and **Show** components, which interpolate an id number into the URL for RESTful routing.

These routes point us to the other three main parent containers: **Home**, **New**, and **Show**. These containers hold multiple child components that will be rendered together, and help to organize a similar data structure amongst them. For example, in my Climate Data Dashboard app, the **Show** container renders three different child components (**Chart**, **Notices**, and **Wonders**). All three components are focused on the same chart object, so it makes sense to group them together into the same container.

### Components
The rest of the components live in the components folder, and come in several varieties. Some are static and stateless, which means they render the same static content every time they are rendered (for example, my **InteractButton** or **NavBar** components). 

### State
Other components are stateful, which means they render dynamic content. This dynamic content can come from their local state (as defined in a constructor method or ES6 state={} syntax), such as in my **DataSourceInput** component, which renders data based on user input into a form.

### Props
In other stateful components, the data comes not from local state within the component itself, but comes from props. Props is data that is passed down from a parent component or container to a child. For example, in my app, there is a **Notices** component that is a parent to the **Notice** component. The Notices component iterates through each notice object in the array, and renders a Notice component with the data for a single notice object for each item. Thus, the single notice object is passed down as props and that data can be individually rendered by each Notice Component on the **Show** page. In addition to data, callback methods can also be passed down as props. For example, in my app the **Wonders** component passes down three action functions (upvoteWonder, downvoteWonder, and deleteWonder) to its child, the **Wonder** component.

### Lifecycle Methods
React has a collection of methods called Lifecycle methods that enable components to react to various events. These methods are organized into mounting, updating, and unmounting phases in the lifecycle. In the mounting phase, constructor methods are run (such as constructing the initial state in my stateful components that include forms), the render() method is run to get JSX elements and render them into HTML on the DOM. We can create a componentDidMount() method to run immediately after the initial render of content. In my app, I use this method to make an initial fetch call to the Rails API to fetch chart data to populate the options in my **DropDownInput** component within the **Home** container. Similarly, we can use other lifecycle methods during the updating and unmounting phase, but I did not need to use any others in this app.

## Redux (coming soon!!!)

### What is Redux? How does it work with React?

### Provider, Store, and Connect

### MapStateToProps and mapDispatchToProps

### Thunk Middleware and Async Actions with the Rails API backend

### Reducers,  Actions, and Dispatches





