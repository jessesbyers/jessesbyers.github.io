---
layout: post
title:      "Climate Data Dashboard: React-Redux Project"
date:       2020-04-14 19:59:18 -0400
permalink:  climate_data_dashboard_react-redux_project
---


*Climate Data Dashboard is a tool for science teachers and students to promote data analysis and productive discussion about data. As a middle school teacher, I was always trying to help my students to examine and compare data across multiple sources. My students needed more practice making observations about the data, as well as generating questions about the data to guide further inquiry. As a teacher, I struggled to find and present appropriate data sources. The Data Climate Dashboard addresses all of these challenges by providing  a  collection of data sources that can displayed together, and providing opportunities for students to interact with the data as well as interact with the ideas of their classmates, which drives discussion and further inquiry.* 

![Imgur](https://i.imgur.com/Hqz3QMC.png)

[Check out the project on Github](https://github.com/jessesbyers/climate-data-dashboard-frontend) and [Watch a video walkthrough](https://drive.google.com/file/d/1IVsYRaElQui7Se3lXT8yAWIy3wBS149a/view?usp=sharing).

## Project Overview
This project was created with a Ruby on Rails backend API which manages all of the teacher and student data related to the charts and observations (notices) and questions (or wonders). The frontend was created as a React-Redux application using React-Router to manage RESTful routing and Thunk to manage asynchronous fetch actions. 

## Managing State in React-Redux
The most challenging aspect of this project was planning how I would manage my data in the backend API as well as in the frountend. I needed to structure my chart and notice/wonder data in the API based on their has_many/belongs_to relationship, and in the first draft of my project, I set up the initial state in my reducer according to this same belongs_to/has_many nested structure. While I was able to create all of my basic functionality using this deeply nested state, it became clear that a complex state structure would cause more difficulty than efficiency. 

### Version 1: Deeply Nested State and a Single Reducer

#### Initial State in the Reducer

As I mentioned, my first draft included a deeply nested initial state in my reducer. Mirroring the relationships in the backend, the state looked something like this:

```
state = {
  charts: [ 
	  {chart1 object },
		{chart2 object },
		{chart2 object },
		...
	]
```

However, the nesting became more complex when we consider the array of notices and wonders that belong to each chart object:

```
state.charts[0] = {
  id: chart_id
  name: chart_name,
  data_url: source_of_raw_data, 
  screenshot_url: url_of_image, 
  notices: [ 
	  {notice1 object},  
		{notice2 object}, 
		{notice3 object}, 
		... 
	], 
  wonders: [ 
	  {wonder1 object},  
	  {wonder2 object},  
	  {wonder3 object}, 
	... 
	]
}
```

Within each notices or wonders array, the objects look like this:

```
state.charts.notices[0] = {
  id: notice_id,
  content: content_text, 
  votes: 7, 
  chart_id: 1
}
```


#### ManageStudentInput Reducer

Putting it all together, although the data was highly structured and organized, it was incredibly difficult to work with, especially in the reducer. Especially when trying to add, delete, and edit notices and wonders, without mutating state.

The reducer started out simple enough for adding and deleting charts, using the spread operator to make sure the original state is not mutated in the process:
```

export default function manageStudentInput(state = {charts: [], requesting: false}, action) {

    let i

    switch (action.type) {
        case 'START_ADDING_CHARTDATA_REQUEST':
            return {
                ...state,
                requesting: true
            }

        case 'ADD_CHARTDATA':
            return {
                charts: state.charts.concat(action.chart),
                requesting: false
            }

        case 'DELETE_CHART':
            return {
                charts: state.charts.filter(chart => chart.id !== action.id), 
                requesting: false
            }    
```

However, the complexity increased significantly when I started managing the actions related to notices and wonders. I had to find each object by its index, and break apart each state object and spread each element in order to add, delete, or update a single property. 

```
        case 'ADD_WONDER':   
        console.log(action)             
            i = state.charts.findIndex(chart => chart.id === action.mutatedWonder.chart_id)

            return {
                ...state,
                charts: [...state.charts.slice(0, i),
                    {
                    ...state.charts[i], wonders: [...state.charts[i].wonders, action.mutatedWonder]
                    },
                    ...state.charts.slice(i + 1)
                ],
                requesting: false
            }
    

        case 'DELETE_NOTICE': 
            i = state.charts.findIndex(chart => chart.id === action.chartId)

            return {
                ...state, 
                charts: [...state.charts.slice(0, i),
                    {...state.charts[i], notices: state.charts[i].notices.filter(notice => notice.id !== action.notice_id)
                    },
                    ...state.charts.slice(i + 1)
                ],
                requesting: false
            }
						
           case 'UPVOTE_WONDER':
               i = state.charts.findIndex(chart => chart.id === action.updatedWonder.chart_id)
    
               return {
                   ...state, 
                   charts: [...state.charts.slice(0, i),
                       {...state.charts[i], wonders: [...state.charts[i].wonders.filter(wonder => wonder.id !== action.updatedWonder.id), action.updatedWonder]
                       },
                       ...state.charts.slice(i + 1)
                   ],
                   requesting: false
               }
```

This is just a taste of the result, you can see the entire 212-line reducer [here](https://github.com/jessesbyers/climate-data-dashboard-frontend/blob/v1-nested_state/src/reducers/manageStudentInput.js). Needless to say, although the app functioned this way, this is not the ideal solution!

![Imgur](https://i.imgur.com/gh9rzcC.png)

### Revised Version: Simple State and Multiple Reducers

#### Initial State in the Reducer

I branched my repository and refactored the entire application with a simplified state, which separated charts, notices, and wonders into separate keys with an array of objects for each. State did not retain the has_many/belongs_to reationships between the models, but it didn't need to since all of the notices and wonders had a foreign key, chart_id.

```
state = {
  charts: [
		{chart1 object },
		{chart2 object },
		{chart2 object },
		...
	], 
	 notices: [ 
	  {notice1 object},  
		{notice2 object}, 
		{notice3 object}, 
		... 
	], 
  wonders: [ 
	  {wonder1 object},  
	  {wonder2 object},  
	  {wonder3 object}, 
	... 
	]
}
```

#### CombineReducers: Charts, Notices, and Wonders

I used combineReducers to manage state for the three different models across individual reducers:

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

By removing the nesting in the initial state, I was able to organize the actions for each model into its own individual file. Better yet, I was able to add, delete, and edit state without manipulating deeply nested data with spread operators, as in my previous example. Actions that would have had return values of 4 lines, have been reduced to 1-liners!

```
export default function chartsReducer(state = [], action) {

    switch (action.type) {
        case 'START_ADDING_CHARTDATA_REQUEST':
            return state

        case 'ADD_CHARTDATA':
            return [...state, action.chart]

        case 'DELETE_CHART':
            return state.filter(chart => chart.id !== action.id)   
            
        case 'START_ADDING_DATA_SOURCE_REQUEST':
            return state

        case 'ADDING_DATA_SOURCE':
            return state

        default:
            return state
    }
}
```

Manipulating data in the notices and wonders reducers had a more significant improvement. A complex code snippet involving slicing and dicing an array by index numbers was greatly simplified, using a simple map function and conditional logic:

```
        case 'DELETE_NOTICE': 
            let remainingNotices = state.map(notice => {
                if (notice.id === action.notice_id) {
                    return action.notice_id
                } else {
                    return notice
                }
            })

            return remainingNotices
```

You can see all of the final reducers [here](https://github.com/jessesbyers/climate-data-dashboard-frontend/tree/master/src/reducers).

![Imgur](https://i.imgur.com/iquIOPZ.png)

## Lessons Learned
Needless to say, this refactoring of my working code was a significant time investment, but it was clear that it needed to be done. I had created a lot of extra work by having an overly complicated nested state structure, and it really didn't gain me any efficiency in my containers and components. Simple state structures are definitely the way to go. That said, working through thew challenge of writing reducer logic with a deeply nested initial state was a tremendous learning opportunity for me. My understanding of the spread operator was shaky before tackling this project, and I had to work through multiple instances of breaking apart data and putting it back together again. I refined my debugging skills and developed a sound process for examining the return values of each action. Both of these skills will certainly come in handy in the future...but not while tackling a deeply nested state. I will definitely be using simple states and combining reducers from now on!

[Check out the project on Github](https://github.com/jessesbyers/climate-data-dashboard-frontend) and [Watch a video walkthrough](https://drive.google.com/file/d/1IVsYRaElQui7Se3lXT8yAWIy3wBS149a/view?usp=sharing).




