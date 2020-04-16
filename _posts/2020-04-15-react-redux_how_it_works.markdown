---
layout: post
title:      "React-Redux: How It Works"
date:       2020-04-15 19:38:06 -0400
permalink:  react-redux_how_it_works
---


*I've completed my project, added comments within my code, written a technical blog post, and recorded my video walkthrough. Now, it's review time. In this blog post, I'll review the core concepts of React and Redux by stepping through my project files and explaining what is happening under the hood. This should be a great way to solidify my own understanding, and I hope that it helps you, too.*

Before you read on, take a quick peek at my last blog post describing my project, the [Climate Data Dashboard](https://jessesbyers.github.io/climate_data_dashboard_react-redux_project), which gives an overview of the project and describes my greatest challenge and lesson learned. If you'd like to follow along with my code, you can check out the [frontend repository here](https://github.com/jessesbyers/climate-data-dashboard-frontend).

![Imgur](https://i.imgur.com/Hqz3QMC.png)

## React
### What is React?
React is a JavaScript library for building user interfaces. It works by rendering static and dynamic components in a virtual DOM which is compared against the actual DOM in the browser in real time. When react detects a change between the previous state (or what is currently rendered in the browser view) and its state in the virtual DOM, it can quickly "react" by re-rendering that DOM element. In order to do this, a react application is built out of a collection of individual component elements that render JSX, which is translated into pure HTML elements for rendering on the DOM. 

### File Structure
At the core of a react app is the public folder and an src folder. The public folder contains the  **index.html**  file, which in turn loads the entire application. The file is a simple template with a head section and a main div element with an id of "root". My file also includes some scripts to support react-bootstrap, which I have used for styling the application.

**index.html**
```
<!DOCTYPE html>
<html lang="en">
  <head>
	
	     // meta and link tags

    <title>Climate Data Dashboard</title>
  </head>
  <body>

    <div id="root"></div>

  </body>
</html>
```

The src folder contains all of the other main application files, including container, component, action, and reducer files (all discussed below). First, the **index.js** file imports a number of packages and functions that will help set up the application within the DOM and organize how data will be managed throughout the app. React and React-DOM allow us to write JSX in our files and enable that JSX to be renedered as html in the DOM, using the createElement method to translate between the languages. The redux imports allow us to create a top-level state that any component can connect to, which makes data management more efficient throughout the app. The Thunk import allows us to use middleware to manage our asynchronous actions, and ensure that the program will wait for fetches to the API to be completed before moving on to the next line of code.

Essentially, the main function of this index.js file is to set up the "store" that will store and update the data for the application, and render the parent component of the app onto the DOM. We use the createStore function to create a store that references our rootReducer as an argument as well as the thunk middleware. It then renders the App component on the DOM at div#root, on the index.html file. This App component is wrapped in the provider, which passes down the data store as props, which will make the data accessible to any child of the app component that requests access to it. See the Redux section below for a code snippet.

### Containers
Next, we have the containers and components folders. These two files contain all of the components, or elements that can be rendered on the DOM. In general, all of these components can take in data from a local state, from props, or from the store, and interpolate that data into the JSX code to render dynamic content. There are different types of components that do this in different ways.

### Routing
My app has four main containers. **App.js** is the parent component that houses all of the other child components in the app. App.js is responsible for all of the routing that happens within the app. It imports the BrowserRouter component from react, and wraps all of the other components within it. The **NavBar** component is a static component rendered on every view, so it is not passed any props. The next four routes are defined with a path as well as a component, and this directs which components will be rendered at a particular time based on the url in the browser's address bar. These paths can be static, such as the paths for the **Home** and **DataSourceInput** components, but can also be dynamic and include nested routes, as in the **New** and **Show** components, which interpolate an id number into the URL for RESTful routing.

**App.js**
```
import { BrowserRouter as Router, Route } from 'react-router-dom';

class App extends Component {

  render() {
    return (
      <Router >
        <Container fluid >
          <div >
            <NavBar />
            <Route exact path="/" component={Home} />
            <Route path={`/charts/new`} component={DataSourceInput} />
            <Route path={`/charts/:id/interact`} component={New} />
            <Route path={`/charts/:id/reflections`} component={Show} />
          </div>
        </Container>
      </Router>
    );
  }
}

export default App;
```

These routes point us to the other three main parent containers: **Home**, **New**, and **Show**. These containers hold multiple child components that will be rendered together, and help to organize a similar data structure amongst them. For example, in my Climate Data Dashboard app, the **Show** container renders three different child components (**Chart**, **Notices**, and **Wonders**). All three components are focused on the same chart object, so it makes sense to group them together into the same container.

### Components
The rest of the components live in the components folder, and come in several varieties. Some are static and stateless, which means they render the same static content every time they are rendered (for example, my **InteractButton** or **NavBar** components). 

### State
Other components are stateful, which means they render dynamic content. This dynamic content can come from their local state (as defined in a constructor method or ES6 state={} syntax), such as in my **ChartInput** component, which renders data based on user input into a form.

**ChartInput.js**
```
class ChartInput extends Component {

    state = {
        notice: {
            content: ""
        },
        wonder: {
            content: ""
        }, 
    }

...

    render() {
            return (
                <div>
                    <Form onSubmit={event => this.handleSubmit(event)}>
                        <Form.Group>
                            <Form.Control size="lg" type="text" name="notice" placeholder="I notice..." value={this.state.notice.content} onChange={event => this.handleChange(event)}/>
                        </Form.Group>
                        
                        <Form.Group>
                            <Form.Control size="lg" type="text" name="wonder" placeholder="I wonder..." value={this.state.wonder.content} onChange={event => this.handleChange(event)}/>
                        </Form.Group>
                        <Button type="submit" >Add</Button>
                    </Form>
                </div>
            );
    }
};
```

### Props
In other stateful components, the data comes not from local state within the component itself, but comes from props. Props is data that is passed down from a parent component or container to a child. For example, in my app, there is a **Notices** component that is a parent to the **Notice** component. The Notices component iterates through each notice object in the array, and renders a Notice component with the data for a single notice object for each item. Thus, the single notice object is passed down as props and that data can be individually rendered by each Notice Component on the **Show** page. In addition to data, callback methods can also be passed down as props. For example, in my app the **Notices** component also passes down three action functions (upvoteWonder, downvoteWonder, and deleteWonder) to its child, the **Notice** component.

**Notices.js**
```
class Notices extends Component {
    render() {
        let sortedNotices = this.props.notices.sort((a, b) => (a.votes < b.votes) ? 1 : -1)
    
        return (
            <div>
                {sortedNotices.map(notice=> <Notice notice={notice} chart={this.props.chart} deleteNotice={this.props.deleteNotice} upvoteNotice={this.props.upvoteNotice} downvoteNotice={this.props.downvoteNotice}/>)}        
            </div>
        );
    }
};
  
export default connect (null, {deleteNotice, upvoteNotice, downvoteNotice})(Notices)
```

### Lifecycle Methods
React has a collection of methods called Lifecycle methods that enable components to react to various events. These methods are organized into mounting, updating, and unmounting phases in the lifecycle. In the mounting phase, constructor methods are run (such as constructing the initial state in my stateful components that include forms), and the render() method is run to get JSX elements and render them into HTML on the DOM. We can create a componentDidMount() method to run immediately after the initial render of content. In my app, I use this method to make an initial fetch call to the Rails API to fetch chart data to populate the options in my **DropDownInput** component within the **Home** container. 

```
    componentDidMount(){
        fetch("http://localhost:3000/charts")
        .then(response => response.json())
        .then(charts => {
          this.setState({charts})
        })
    }
```

Similarly, we can use other lifecycle methods during the updating and unmounting phase, but I did not need to use any others in this app.

## Redux (coming soon!!!)

### What is Redux? How does it work with React?
Redux is a code library that works within React to manage state scross the entire application. At its core, it allows us to represent the state for our application as a single JavaScript object, that can be accessed by any component it is connected to and whose values can be accessed  using typical methods for accessing values in objects and arrays. 

### Reducers,  Actions, and Dispatches
The Redux state is read-only, and can only be manipulated or updated by dispatching actions, which are JavaScript objects that include a type key/value pair, and a payload of data. These actions are interpreted and carried out by a reducer function, which is a pure function takes the previous state of the data's application and returns the next state of the data's application. In this example, the action type is "DELETE_CHART" and the data payload is the chart-to-be-deleted's id number.

**Action: deleteChart.js**
```
export const deleteChart = (chart) => {
        return {
            type: 'DELETE_CHART',
            id: chart.id
        }
}
```


**Reducer:** **rootReducer.js**
```
import { combineReducers } from 'redux'

import chartsReducer from './chartsReducer'
import noticesReducer from './noticesReducer'
import wondersReducer from './wondersReducer'

const rootReducer = combineReducers({
    charts: chartsReducer,
    notices: noticesReducer,
    wonders: wondersReducer
});

export default rootReducer
```

**Reducer:** **chartsReducer.js**
```
export default function chartsReducer(state = [], action) {

    switch (action.type) {

        case 'DELETE_CHART':
            return state.filter(chart => chart.id !== action.id)   

        default:
            return state
    }
}
```

### Provider and Store
As discussed above, we can set up our application to use redux to manage state through the **index.js** file (copied below).

*The index.js file imports a number of packages and functions that will help set up the application within the DOM and organize how data will be managed throughout the app. React and React-DOM allow us to write JSX in our files and enable that JSX to be renedered as html in the DOM, using the createElement method to translate between the languages. The redux imports allow us to create a top-level state that any component can connect to, which makes data management more efficient throughout the app. The Thunk import allows us to use middleware to manage our asynchronous actions, and ensure that the program will wait for fetches to the API to be completed before moving on to the next line of code.*

*Essentially, the main function of this index.js file is to set up the "store" that will store and update the data for the application, and render the parent component of the app onto the DOM. We use the createStore function to create a store that references our rootReducer as an argument as well as the thunk middleware. It then renders the App component on the DOM at div#root, on the index.html file. This App component is wrapped in the provider, which passes down the data store as props, which will make the data accessible to any child of the app component that requests access to it.*

```
import React from 'react';
import ReactDOM from 'react-dom';

import { Provider } from 'react-redux';
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';

import App from './containers/App';
import rootReducer from './reducers/rootReducer'

const store = createStore(rootReducer, applyMiddleware(thunk))

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

### Connect, MapStateToProps, and mapDispatchToProps
Once the Provider is set up and is passing the store as props to the rest of the application, we can use the connect() function in individual components to connect with the store (access data) and respond to changes in state (by re-rendering that component when the state changes). In order
 to use the connect function, we have to import it into the component, as I have done in the **DropdownInput** component.
 
 `import { connect } from 'react-redux'`
 
 The mapStateToProps function is then written to specify which parts of the the state need to be accessed by the component, and how they will be called within the component. For example, in this component, since I only need to access the charts, my function can be written like this:
 
 `const mapStateToProps = (state) => {
    return {
        charts: state.charts
    }    
}`

This sets a key in the components props of "charts" which is set equal to the value of the state's charts key. To access this data in this component, we can use {this.props.charts}.

If we need to access actions from the component, we need to use the mapDispatchTo Props function, which includes the action(s) to be made available to the component through props. The longhand version of the function looks like this:

```
const mapDispatchToProps = dispatch => {
  return {
    deleteChart: () => { dispatch(deleteChart()) }
  }
}
```

...and multiple dispatch actions can be included in the same function in this way:

```
const mapDispatchToProps = dispatch => {
  return {
    deleteChart: () => { dispatch(deleteChart()) },
		fetchChartData: () => { dispatch(fetchChartData()) },
		fetchNotices: () => { dispatch(fetchNotices()) },
	  fetchWonders: () => { dispatch(fetchWonders()) }
  }
}
```

Then, we use the connect function in the export function at the bottom of the file to complete the connection:

```
export default connect (mapStateToProps, mapDispatchToProps)(DropdownInput);
```

However, we can use a shorthand to replace writing out the mapStateToProps and mapDispatchToProps functions, and just use the shorthand below:

```
export default connect (state => ({ charts: state.charts }), { fetchChartData, fetchNotices, fetchWonders, deleteChart} )(DropdownInput);
```

### Thunk Middleware and Async Actions with the Rails API backend
In the React Lifecycle Methods section above, I included a code snippet for a fetch get request to populate the **DropdownInput** component with all of the charts currently in Rails API. By calling the fetch within the componentDidMount() method, it was able to fetch the data and then re-render quickly after the DOM had loaded. It is almost impossible to see, but with that approach, the browser renders an empty dropdown menu, fetches the data, and then re-renders the page with the populated options. 

In redux, we can manage our asynchronous fetch requests of all types through our dispatched actions, which allows us to keep the code out of the component so it can be more focused on presentation. We use the thunk middleware imported into the **index.js** file to set up this capability, and then pass thunk in as an argument in the createStore method.

```
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';

import rootReducer from './reducers/rootReducer'

const store = createStore(rootReducer, applyMiddleware(thunk))
```

Once this set up is complete, the rest of the fetch code is set up as a dispatched action and called in the connect function of the component that will trigger the fetch call. For example, in my DataSourceInput component, a user fills out a form with information about a new data source to be added to the database. The action, addDataSource, is imported into the component, and mapped to props through the connect function at the bottom of the file. The action is dispatched through an event listener on the form, which triggers the action and the fetch post request to the API. When the fetch is complete, the data returned from from the fetch is passed to the reducer and is used to modify the state.

**Component: DataSourceInput.js** *(only relevant code included)*
```
import { addDataSource} from '../actions/addDataSource'
import { connect } from 'react-redux'

class DataSourceInput extends Component {

    state = {
        name: "",
        data_url: "",
        screenshot_url: "",
        visible: false
    }

    handleSave = () => {
        this.props.addDataSource({name: this.state.name, data_url: this.state.data_url, screenshot_url: this.state.screenshot_url})
        this.setState({
            name: "",
            data_url: "",
            screenshot_url: "", 
        })
    }

    render() {
		         ...
						 return (
          
                <Card>
                    <Card.Header>Confirm your New Data Source</Card.Header>
                    <Card.Text>
                        <Button type="submit" onClick={this.handleSave}>Save Data Source to Database</Button>
                        <Button type="submit" variant="danger" onClick={this.handleCancel}>Cancel</Button>
                    </Card.Text>
                    <Chart chart={this.state}/>
                </Card>
              )
      }        
				
export default connect (null, {addDataSource})(DataSourceInput)

```

**Action: addDataSource.js**

export const addDataSource = (dataSource) => {

    return dispatch => {
        dispatch({ type: 'START_ADDING_DATA_SOURCE_REQUEST' })
        
        let configObj = {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                "Accept": "application/json"
            },
            body: JSON.stringify(dataSource)
        };

        fetch(`http://localhost:3000/charts`, configObj)
        .then(function(response) {
            return response.json()
        })
				
        .then(data => {
            dispatch({ type: 'ADD_DATA_SOURCE', data })})

        .catch(function(error) {
            alert("ERROR! Please Try Again");
            console.log(error.message);
        });
			}
    }

**Rails charts_controller.rb

```
    def create
        @chart = Chart.new(chart_params)
        if @chart.save
            render json: @chart.to_json(:except => [:updated_at, :created_at])
        else
            render json: {error: "Chart cannot be saved to the database. Please try again."}
        end
    end
```


**Reducer: chartsReducer.js**

```
export default function chartsReducer(state = [], action) {

    switch (action.type) {
        case 'START_ADDING_DATA_SOURCE_REQUEST':
            return state
        // new chart is added to the database, but not added to state.charts until it is loaded on the dashboard, therefore we are just returning state without updating it
        case 'ADDING_DATA_SOURCE':
            return state

        default:
            return state
    }
}
```

Well, that should just about cover the basics! I hope reading this review is as helpful to you as it was for me writing it.

Again, if you want to learn more about the project and see it in action:
* [Check out the project on Github](https://github.com/jessesbyers/climate-data-dashboard-frontend) and [Watch a video walkthrough](https://drive.google.com/file/d/1IVsYRaElQui7Se3lXT8yAWIy3wBS149a/view?usp=sharing).

* Read my first blog post about the project: [Climate Data Dashboard: React-Redux Project](https://jessesbyers.github.io/climate_data_dashboard_react-redux_project )









