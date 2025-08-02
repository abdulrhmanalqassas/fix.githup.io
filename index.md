# Plugin Development Guide for Junior Developers

## What is This Plugin and Why Do We Need It?

The `test-dev-basem` plugin is a **spatial analysis tool** that demonstrates how to create interactive map-based applications. This plugin serves as a foundation for building geospatial solutions that allow users to:

### 🎯 **Primary Purpose:**
- **Interactive Map Analysis**: Enable users to click on a map and perform spatial queries
- **Buffer Analysis**: Create circular zones around selected points to analyze nearby features
- **Data Visualization**: Display query results in an organized, searchable format
- **Real-time Interaction**: Provide immediate feedback and results as users interact with the map

### 🌟 **Real-World Applications:**
- **Urban Planning**: Find all buildings, roads, or utilities within a certain distance of a proposed development
- **Emergency Response**: Identify all hospitals, fire stations, or evacuation centers within a radius of an incident
- **Market Analysis**: Locate all competitors, customers, or points of interest near a business location
- **Environmental Studies**: Analyze features within a buffer zone around sensitive areas
- **Transportation Planning**: Find all bus stops, parking areas, or traffic lights within walking distance

### 📊 **Expected Outcomes:**
After implementing this plugin, users will be able to:
1. Click anywhere on the map to select a point of interest
2. Automatically generate a buffer zone around that point
3. Query and retrieve all geographic features within that buffer
4. View results in a structured table with filtering and sorting capabilities
5. Interact with individual results (zoom to feature, highlight on map)

### 🏗️ **Technical Learning Objectives:**
This plugin teaches you how to:
- Build modular, reusable React components for map applications
- Implement Redux state management for complex data flows
- Integrate with mapping APIs and spatial libraries
- Handle asynchronous operations and API calls
- Create responsive user interfaces for data visualization
- Follow best practices for plugin architecture

## Table of Contents
1. [Project Structure](#project-structure)
2. [Setup and Configuration](#setup-and-configuration)
3. [Core Components](#core-components)
4. [Redux Implementation](#redux-implementation)
5. [Services](#services)
6. [Step-by-Step Implementation](#step-by-step-implementation)

## Project Structure

### 📁 **Understanding the Plugin Architecture**

A plugin is essentially a **self-contained module** that extends the functionality of a larger application. Think of it like a puzzle piece that fits perfectly into the main system while providing specific capabilities.

```
my-plugin/
├── .babelrc                    # JavaScript transpilation rules
├── .gitignore                  # Files to ignore in version control
├── package.json                # Project metadata and dependencies
├── src/                        # Source code directory
│   ├── index.js               # Plugin entry point - exports everything
│   ├── messages.js             # Text and translations for UI
│   ├── plugin.svg             # Visual icon for the plugin
│   ├── constants/
│   │   └── constants.js       # Shared values used across the plugin
│   ├── components/
│   │   ├── MainComponent/     # The primary interactive component
│   │   └── ResultComponent/   # Displays results and handles user input
│   ├── actions/
│   │   └── index.js           # Redux actions - what can happen
│   ├── reducers/
│   │   └── index.js           # Redux reducers - how state changes
│   ├── selectors/
│   │   └── index.js           # Redux selectors - how to read state
│   └── services/
│       └── queryService.js    # External API communication
└── test-data/
    ├── plugin.json            # Plugin registration information
    └── test-settings.json     # Development and testing configuration
```

### 🧩 **How Components Work Together:**

1. **index.js** - The "front door" of your plugin. It exports all the pieces that the main application needs to know about.

2. **Components** - The visual and interactive parts:
   - **MainComponent**: Acts as a controller, manages when things happen
   - **ResultComponent**: Shows the user interface and handles user interactions

3. **Redux Layer** - Manages data flow:
   - **Actions**: Define what events can occur ("user clicked map", "data loaded")
   - **Reducers**: Define how those events change the application state
   - **Selectors**: Provide easy ways to read specific parts of the state

4. **Services** - Handle external communication:
   - **queryService**: Communicates with map servers to get geographic data

5. **Support Files**:
   - **messages.js**: Contains all text shown to users (supports multiple languages)
   - **constants.js**: Stores values that don't change (like plugin name)
   - **plugin.svg**: The icon users see in the interface

## Setup and Configuration



## Core Components

cet also ## Settings Design

In this plugin, we will interact with a single layer of points. Hence, we will use the single layer picker field from the configuration settings. We will also need the ability to add a new point on the map. We want the add form to be dynamic. Hence, we will use the Nested designer field from the configuration settings as our form body and add all the dynamic fields that we want from there. The client might also need to draw the points in a certain way - colored point or a certain image - so we can add a radio group to the settings to give him the ability to choose what style he needs on the map.

**Single Layer Picker Field**: For selecting the layer.

## Plugin Design

Now let us think about the plugin design.

What should we add to the plugin body container?

If we think about it we need to display the points of the selected layer on load to a table-like format and enable some functions on each feature.

We have a ready made component called "Penta-Grid" that can handle that for us. We will use it and it should look something like this.

After finishing the tasks from 1A to 2B and configuring the plugin we now have our web browser open, the code open and the plugin configurated in our code editor.

In MapClick.js in the componentDidMount section, we will log the settings using the console.log function like this:

Now if we open the developer tools > console from our browser to check the settings we will find it like this:

**Note**: Do not access localhost:3001. You need to run the sandbox and inspect the plugin there. This is not a standalone React app; the plugin is a file that is part of a React application.

### 1. Constants

Create `src/constants/constants.js`:

```javascript
export const LOCALIZATION_NAMESPACE = "my-plugin";
```

### 2. Main Entry Point

Create `src/index.js`:

```javascript
import MainComponent from "./components/MainComponent/MainComponent.component";
import ResultComponent from "./components/ResultComponent/ResultComponent.component";
import defaultLocalization from "./messages";
import { LOCALIZATION_NAMESPACE } from "./constants/constants";

import myPluginReducer from "./reducers";
import * as myPluginActions from "./actions";
import * as myPluginSelectors from "./selectors";

// Set component metadata
MainComponent.Title = LOCALIZATION_NAMESPACE + ":title";
MainComponent.Icon = LOCALIZATION_NAMESPACE + ":icon";

// Export plugin structure
const components = {
  MainComponent,
  ResultComponent,
};

const localization = {
  namespace: LOCALIZATION_NAMESPACE,
  defaultLocalization,
};

const reducers = { myPluginReducer };
const actions = { ...myPluginActions };
const selectors = { ...myPluginSelectors };

export { components, localization, reducers, actions, selectors };
```



## Redux Implementation

### 1. Actions

Create `src/actions/index.js`:

```javascript
import { LOCALIZATION_NAMESPACE } from "../constants/constants";

// Action types
export const SET_FEATURES = `${LOCALIZATION_NAMESPACE}_SET_FEATURES`;
export const SET_LOADING = `${LOCALIZATION_NAMESPACE}_SET_LOADING`;

// Action creators
export const setFeatures = (features) => ({
  type: SET_FEATURES,
  payload: features,
});

export const setLoading = (loading) => ({
  type: SET_LOADING,
  payload: loading,
});
```

### 2. Reducers

Create `src/reducers/index.js`:

```javascript
import * as actions from "../actions";

const INITIAL_STATE = {
  features: [],
  loading: false,
};

const reducer = (state = INITIAL_STATE, action) => {
  switch (action.type) {
    case actions.SET_FEATURES:
      return {
        ...state,
        features: [...action.payload],
      };
    case actions.SET_LOADING:
      return {
        ...state,
        loading: action.payload,
      };
    default:
      return { ...state };
  }
};

export default reducer;
```

### 3. Selectors

Create `src/selectors/index.js`:

```javascript
export const selectFeatures = (state) => state.myPluginReducer.features;
export const selectLoading = (state) => state.myPluginReducer.loading;
```

## Services

## Fetching the Points from the Layer

**Note**: Don't forget to refer to the queryFeature docs for additional reference.

In this section, we will fetch the points that we want to display from our database. First we need to create the special query function that we will use - it will take the layer from the settings and return the features.

We will create a new folder inside the src folder and name it **services**. Inside the services folder we will create a new file and name it **queryService.js** - we will write our query code there to use it later in various places.

Initially, we must import the following from ma-lib:

```javascript
import {
  store,
  query,
  systemShowLoading,
  systemHideLoading,
} from "@penta-b/ma-lib";
```

### Query Service

Create `src/services/queryService.js`:

```javascript
import {
  store,
  query,
  systemShowLoading,
  systemHideLoading,
} from "@penta-b/ma-lib";

const genQueryBody = (layer, bufferGeo) => {
  return [
    {
      dataSource: {
        id: layer.id,
      },
      filter: {
        conditionList: [
          {
            spatialCondition: {
              key: layer.geometryField.fieldName,
              geometry: JSON.stringify(bufferGeo),
              spatialRelation: "INTERSECT",
            },
          },
        ],
        logicalOperation: "AND",
      },
      crs: layer.crs,
    },
  ];
};

export const callQueryService = async (layer, bufferGeo) => {
  store.dispatch(systemShowLoading());
  try {
    const response = await query.queryFeatures(genQueryBody(layer, bufferGeo));
    console.log("Query response:", response);
    return JSON.parse(response.data[0].features).features;
  } catch (error) {
    console.error("Query error:", error);
    return [];
  } finally {
    store.dispatch(systemHideLoading());
  }
};
```

This file contains 2 functions: **callQueryService** and **genQueryBody**. We will discuss them in detail.

### genQueryBody Function

This function simply takes the layer settings and generates the query body that we will use to get the features from the DB. It constructs a proper query structure with:
- **dataSource**: Specifies which layer to query using the layer ID
- **filter**: Defines spatial conditions for filtering features
- **spatialCondition**: Sets up geometric intersection queries
- **crs**: Coordinate reference system for proper spatial calculations

### callQueryService Function

At first, we dispatch the **systemShowLoading** event to make the loader appear on the screen - this is good to let the user see that there is a process loading.

Then we call the **query.queryFeatures** with the **genQueryBody** function we just explained.

From the response, we extract features and return them. If there is an error we log it and return an empty array, and finally we close the loading screen to tell the user that the loading is finished.

**Workflow Summary**:
1. **Show Loading**: Display loading indicator to user
2. **Generate Query**: Create proper query body with spatial filters
3. **Execute Query**: Call the query service with generated parameters
4. **Process Response**: Extract and parse features from response
5. **Error Handling**: Log errors and return empty array if query fails
6. **Hide Loading**: Remove loading indicator when process completes

## Drawing Features on the Map

### The drawFeatures Function

This function initially calls the **validateVL** function and after the validation we convert all the **GEOJSONFeatures** that we get into **olFeatures**. After that, we generate the style using the **generateStyle** function. Finally, we use the **setStyle** function and pass the style as a parameter and the **addFeatures** function and pass the features as a parameter to add the features and apply the desired style to them.

```javascript
export const drawFeatures = async (features, customSettings) => {
  try {
    // Validate vector layer
    const vectorLayer = await validateVL();
    
    // Convert GeoJSON features to OpenLayers features
    const olFeatures = features.map(feature => {
      const olFeature = new Feature({
        geometry: new Point(feature.geometry.coordinates),
        properties: feature.properties
      });
      return olFeature;
    });
    
    // Generate style for features
    const style = generateStyle(customSettings);
    
    // Apply style to vector layer
    vectorLayer.setStyle(style);
    
    // Add features to the layer
    vectorLayer.addFeatures(olFeatures);
    
    return olFeatures;
  } catch (error) {
    console.error('Error drawing features:', error);
    return [];
  }
};
```

### Combining callQueryService & drawFeatures Functions

Now let us combine the **callQueryService** & the **drawFeatures** functions. In **MapClick.js** in the **componentDidMount**, we write this code:

```javascript
componentDidMount() {
  // Get the layer settings from the dataSettings
  const layerSettings = this.props.dataSettings.layer;
  
  // Get the configurable settings from the layer's basicSettings
  const customSettings = this.props.dataSettings.basicSettings;
  
  // Call the query service with layer settings
  this.queryAndDrawFeatures(layerSettings, customSettings);
}

queryAndDrawFeatures = async (layerSettings, customSettings) => {
  try {
    // Call the query service
    const features = await callQueryService(layerSettings, this.bufferGeometry);
    
    // Check if there is an error with the request
    if (!features || features.length === 0) {
      this.props.notify('No features found in the selected area', 'warning');
      return;
    }
    
    // If there are no errors, call the drawFeatures function
    const drawnFeatures = await drawFeatures(features, customSettings);
    
    // Save features to Redux store
    this.props.setFeatures(drawnFeatures);
    
  } catch (error) {
    console.error('Error in query and draw process:', error);
    this.props.notify('Error occurred while fetching data', 'error');
  }
};
```

We get the layer settings from the **dataSettings** and we get the configurable settings from the layer's **basicSettings**. After that we call the **callQueryService** with the Layer settings and check if there is an error with the request we return and we show a notification to the user using the **notify** function which is defined in the **mapDispatchToProps**.

After that if there are no errors we call the **drawFeatures** function with the response and pass our custom settings to it.

## Redux State Management Implementation

We will need to save the features that we queried in the last section to Redux by doing the following:

### Setting Up Redux Architecture

Create these 3 folders inside the **src** folder: **"actions"**, **"selectors"**, **"reducers"**
Create an **index.js** file inside each of these folders.

### Actions Implementation

In the **actions/index.js** file type the following:

```javascript
// Action Types
export const SET_FEATURES = 'SET_FEATURES';
export const CLEAR_FEATURES = 'CLEAR_FEATURES';
export const SET_LOADING = 'SET_LOADING';

// Action Creators
export const setFeatures = (features) => ({
  type: SET_FEATURES,
  payload: features
});

export const clearFeatures = () => ({
  type: CLEAR_FEATURES
});

export const setLoading = (isLoading) => ({
  type: SET_LOADING,
  payload: isLoading
});
```

### Reducer Implementation

In the **reducer/index.js** file type the following:

```javascript
import { SET_FEATURES, CLEAR_FEATURES, SET_LOADING } from '../actions';

const initialState = {
  features: [],
  isLoading: false,
  error: null
};

const featuresReducer = (state = initialState, action) => {
  switch (action.type) {
    case SET_FEATURES:
      return {
        ...state,
        features: action.payload,
        isLoading: false,
        error: null
      };
    case CLEAR_FEATURES:
      return {
        ...state,
        features: [],
        error: null
      };
    case SET_LOADING:
      return {
        ...state,
        isLoading: action.payload
      };
    default:
      return state;
  }
};

export default featuresReducer;
```

### Selectors Implementation

In the **selector/index.js** file type the following:

```javascript
// Feature Selectors
export const selectFeatures = (state) => state.features || [];
export const selectFeaturesCount = (state) => (state.features || []).length;
export const selectIsLoading = (state) => state.isLoading || false;
export const selectError = (state) => state.error;

// Derived Selectors
export const selectHasFeatures = (state) => selectFeaturesCount(state) > 0;
export const selectFeaturesWithProperties = (state) => {
  return selectFeatures(state).filter(feature => 
    feature.properties && Object.keys(feature.properties).length > 0
  );
};
```

### Main Index Export

And inside the **index.js** that is inside the **src** we need to import and export all of these parts. Like this:

```javascript
// Export actions
export * from './actions';

// Export selectors
export * from './selectors';

// Export reducer
export { default as featuresReducer } from './reducers';

// Export components
export { default as MainComponent } from './components/MapClick/MapClick.component';
export { default as ResultComponent } from './components/MapClickResult/MapClickResult.component';

// Export services
export * from './services/queryService';
```

Now we can save the features that we got from the **queryFeature** request and consume it elsewhere.

### Using Redux in Components

In the **MapClick.js** in the **mapDispatchToProps** we will add the **setFeatures** function:

```javascript
const mapDispatchToProps = (dispatch) => {
  return {
    setFeatures: (features) => dispatch(setFeatures(features)),
    clearFeatures: () => dispatch(clearFeatures()),
    setLoading: (isLoading) => dispatch(setLoading(isLoading)),
    notify: (message, type) => dispatch(notify(message, type)),
    showTriggerResult: (props, onAdd) =>
      dispatch(
        actionsRegistry.getActionCreator(
          "showComponent",
          "my-plugin",
          "MapClickResult",
          props,
          onAdd
        )
      ),
    removeComponent: (id) =>
      dispatch(actionsRegistry.getActionCreator("removeComponent", id)),
  };
};
```

We will use it in the query function that we wrote before:

```javascript
queryAndDrawFeatures = async (layerSettings, customSettings) => {
  try {
    // Set loading state
    this.props.setLoading(true);
    
    // Call the query service
    const features = await callQueryService(layerSettings, this.bufferGeometry);
    
    if (!features || features.length === 0) {
      this.props.notify('No features found in the selected area', 'warning');
      this.props.clearFeatures();
      return;
    }
    
    // Draw features and save to Redux
    const drawnFeatures = await drawFeatures(features, customSettings);
    this.props.setFeatures(drawnFeatures);
    
    this.props.notify(`Found ${drawnFeatures.length} features`, 'success');
    
  } catch (error) {
    console.error('Error in query and draw process:', error);
    this.props.notify('Error occurred while fetching data', 'error');
    this.props.clearFeatures();
  } finally {
    this.props.setLoading(false);
  }
};
```

## Using Penta-Grid to Display the Points

**Note**: Don't forget to refer to the PentaGrid docs for detailed configuration options.

Now let us continue where we left off. After making the trigger logic, we didn't tell you what we will use it for. Now we will continue. The trigger logic part was to enable the opening and closing of the window that our grid will be in.

### Installing Penta-Grid

Initially, we will need to install the grid by opening the console and redirecting to our project then typing this command:

```bash
npm i @penta-b/grid
```

Wait for the package to install. Some of you might get an error while downloading the package. This happened because you need to add a new file called **'.npmrc'** and type this line inside it:

```
//registry.npmjs.org/:_authToken=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
```

### Implementing Penta-Grid in ResultComponent

After installing the grid, we need to implement it in our **MapClickResult** component:

```javascript
import React from 'react';
import { connect } from 'react-redux';
import { components } from '@penta-b/grid';
import { selectFeatures, selectIsLoading } from '../../selectors';

const { Grid } = components;

class MapClickResult extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      gridConfig: {
        columns: [
          {
            field: 'id',
            headerName: 'ID',
            width: 100,
            sortable: true
          },
          {
            field: 'name',
            headerName: 'Name',
            width: 200,
            sortable: true
          },
          {
            field: 'type',
            headerName: 'Type',
            width: 150,
            sortable: true
          },
          {
            field: 'coordinates',
            headerName: 'Coordinates',
            width: 250,
            valueGetter: (params) => {
              const coords = params.row.geometry?.coordinates;
              return coords ? `${coords[0].toFixed(6)}, ${coords[1].toFixed(6)}` : 'N/A';
            }
          }
        ],
        pagination: true,
        pageSize: 10,
        sortingOrder: ['asc', 'desc'],
        disableSelectionOnClick: false
      }
    };
  }

  // Transform features data for grid display
  transformFeaturesForGrid = (features) => {
    return features.map((feature, index) => ({
      id: feature.properties?.id || index,
      name: feature.properties?.name || `Feature ${index + 1}`,
      type: feature.properties?.type || 'Unknown',
      geometry: feature.geometry,
      ...feature.properties // Spread all other properties
    }));
  };

  handleRowClick = (params) => {
    console.log('Row clicked:', params.row);
    // You can add logic here to highlight the feature on the map
    // or show detailed information
  };

  render() {
    const { features, isLoading } = this.props;
    const gridData = this.transformFeaturesForGrid(features);

    return (
      <div style={{ height: '400px', width: '100%', padding: '10px' }}>
        <h3>Query Results</h3>
        {isLoading ? (
          <div>Loading...</div>
        ) : (
          <>
            <p>Found {features.length} features</p>
            {features.length > 0 ? (
              <Grid
                rows={gridData}
                columns={this.state.gridConfig.columns}
                pageSize={this.state.gridConfig.pageSize}
                pagination={this.state.gridConfig.pagination}
                sortingOrder={this.state.gridConfig.sortingOrder}
                disableSelectionOnClick={this.state.gridConfig.disableSelectionOnClick}
                onRowClick={this.handleRowClick}
                checkboxSelection
                disableRowSelectionOnClick={false}
              />
            ) : (
              <div>No features found. Try clicking on a different area of the map.</div>
            )}
          </>
        )}
      </div>
    );
  }
}

const mapStateToProps = (state) => ({
  features: selectFeatures(state),
  isLoading: selectIsLoading(state)
});

export default connect(mapStateToProps)(MapClickResult);
```

### Grid Configuration Options

The Penta-Grid component offers many configuration options:

- **columns**: Define what data to show and how to format it
- **pagination**: Enable/disable pagination for large datasets
- **pageSize**: Number of rows per page
- **sortingOrder**: Allow users to sort data
- **checkboxSelection**: Enable row selection with checkboxes
- **onRowClick**: Handle when user clicks on a row
- **disableSelectionOnClick**: Control selection behavior

### Advanced Grid Features

You can enhance the grid with additional features:

```javascript
// Custom cell renderers
const customColumns = [
  {
    field: 'status',
    headerName: 'Status',
    width: 120,
    renderCell: (params) => (
      <span style={{ 
        color: params.value === 'active' ? 'green' : 'red',
        fontWeight: 'bold'
      }}>
        {params.value}
      </span>
    )
  },
  {
    field: 'actions',
    headerName: 'Actions',
    width: 150,
    renderCell: (params) => (
      <button onClick={() => this.viewDetails(params.row)}>
        View Details
      </button>
    )
  }
];
```

## Step-by-Step Implementation

### Step 1: Initialize Project

```bash
# Create project directory
mkdir my-plugin
cd my-plugin

# Initialize npm
npm init -y

# Install dependencies
npm install --save react react-dom react-redux redux @penta-b/grid @penta-b/mna-penta-smart-forms @turf/turf
npm install --save-dev @babel/core @babel/preset-env @babel/preset-react babel-loader webpack webpack-cli webpack-dev-server cross-env copy-webpack-plugin clean-webpack-plugin css-loader style-loader file-loader
```

### Step 2: Create Directory Structure

```bash
mkdir src
mkdir src/components
mkdir src/components/MainComponent
mkdir src/components/ResultComponent
mkdir src/actions
mkdir src/reducers
mkdir src/selectors
mkdir src/services
mkdir src/constants
mkdir test-data
```

### Step 3: Create Main Component

Create `src/components/MainComponent/MainComponent.component.js`:

```javascript
import React from "react";
import { connect } from "react-redux";
import { selectorsRegistry, actionsRegistry } from "@penta-b/ma-lib";

class MainComponent extends React.Component {
  constructor(props) {
    super(props);
  }

  componentDidUpdate(prevProps) {
    if (this.props.isActive && prevProps.isActive !== this.props.isActive) {
      // Show the result component when activated
      this.props.showResultComponent(
        {
          settings: this.props.settings,
        },
        (id) => {
          this.id = id;
        }
      );
    } else if (!this.props.isActive) {
      // Clean up when deactivated
      this.id && this.props.removeResultComponent(this.id);
    }
  }

  render() {
    return null; // This component doesn't render UI directly
  }
}

const mapStateToProps = (state, ownProps) => {
  return {
    // Map any needed state here
  };
};

const mapDispatchToProps = (dispatch) => {
  return {
    showResultComponent: (props, onAdd) =>
      dispatch(
        actionsRegistry.getActionCreator(
          "showComponent",
          "my-plugin",
          "ResultComponent",
          props,
          onAdd
        )
      ),
    removeResultComponent: (id) =>
      dispatch(actionsRegistry.getActionCreator("removeComponent", id)),
  };
};

export default connect(mapStateToProps, mapDispatchToProps)(MainComponent);
```

## Implementing the Trigger Logic to Open and Close the Sidebar

First, we need to go to the MapClick.js file and change the code inside the componentDidUpdate. To be like this:

```javascript
componentDidUpdate(prevProps) {
  if (this.props.isActive && prevProps.isActive !== this.props.isActive) {
    // Show the result component when activated
    this.props.showTriggerResult(
      {
        settings: this.props.settings,
      },
      (id) => {
        this.id = id;
      }
    );
  } else if (!this.props.isActive) {
    // Clean up when deactivated
    this.id && this.props.removeComponent(this.id);
  }
}
```

What this block does is that it checks if the trigger is activated and then removes the previous component and shows the MapClickResult using the function `showTriggerResult`. This function is defined in mapDispatchToProps.

### Understanding the showTriggerResult Function

It takes the following parameters:
- **The action name**: "showComponent" action that injects the component "3rd parameters" in its allocated place in the application configurator
- **The namespace**: this is the plugin's namespace
- **The component to be injected**: this will be "MapClickResult" in your codebase
- **Props to be injected in your component**
- **onAdd**: callback function to be called when the component is mounted and has componentID as a parameter
- **onRemove**: callback function to be called when the component unmounts

When we call `showTriggerResult` we pass no props and save the Id in the onAdd function. And in this case we did not include the onRemove function.

If the `props.isActive` is false then we execute the else which is simply checking if the component is mounted and we save the id then we simply remove it.

We will now see the remove function which is also defined in the mapDispatchToProps section:

```javascript
const mapDispatchToProps = (dispatch) => {
  return {
    showTriggerResult: (props, onAdd) =>
      dispatch(
        actionsRegistry.getActionCreator(
          "showComponent",
          "my-plugin",
          "MapClickResult",
          props,
          onAdd
        )
      ),
    removeComponent: (id) =>
      dispatch(actionsRegistry.getActionCreator("removeComponent", id)),
  };
};
```

### Step 4: Create Result Component

Create `src/components/ResultComponent/ResultComponent.component.js`:

```javascript
import React from "react";
import { connect } from "react-redux";
import { components } from "@penta-b/grid";
import { withLocalize, apiRegistry } from "@penta-b/ma-lib";
import * as turf from "@turf/turf";
import { callQueryService } from "../../services/queryService";
import { selectFeatures } from "../../selectors";
import { setFeatures } from "../../actions";

const { Grid } = components;

class ResultComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      sizeBuffer: "1000",
      uniteBuffer: "meters",
    };
  }

  componentDidMount() {
    this.initializeDrawing();
  }

  componentWillUnmount() {
    this.cleanup();
  }

  cleanup() {
    if (this.drawing) {
      this.props.removeInteraction(this.drawing);
    }
    if (this.vectorLayer) {
      this.props.removeVectorLayer(this.vectorLayer);
    }
  }

  async initializeDrawing() {
    try {
      const [Drawing, VectorLayer] = await apiRegistry.getApis([
        "Drawing",
        "VectorLayer",
      ]);

      this.vectorLayer = new VectorLayer();
      this.props.addVectorLayer(this.vectorLayer);

      this.drawing = new Drawing({
        type: "point",
        vectorLayer: this.vectorLayer,
      });

      this.props.addInteraction(this.drawing);
      this.drawing.setOnDrawFinish(this.handleDrawFinish);
    } catch (error) {
      console.error("Failed to initialize drawing:", error);
    }
  }

  handleDrawFinish = async (feature) => {
    this.vectorLayer.clear();

    try {
      // Create buffer around the drawn point
      const buffered = turf.buffer(
        feature.getGeometry(),
        parseFloat(this.state.sizeBuffer) || 0,
        { units: this.state.uniteBuffer }
      );

      // Style the buffer
      const [Feature, Fill, Style, Stroke] = await apiRegistry.getApis([
        "Feature",
        "Fill",
        "Style",
        "Stroke",
      ]);

      const featureStyle = new Style(
        new Fill("#8400004A"),
        new Stroke("pink", 1)
      );

      const featureBuffer = new Feature(buffered, featureStyle);

      // Add features to map
      this.vectorLayer.addFeature(feature);
      this.vectorLayer.addFeature(featureBuffer);

      // Query data within buffer (if you have a data layer)
      // const queryResults = await callQueryService(dataLayer, featureBuffer.getGeometry());
      // this.props.setFeatures(queryResults);
    } catch (error) {
      console.error("Error processing draw finish:", error);
    }
  };

  render() {
    const { features } = this.props;

    return (
      <div style={{ padding: "10px" }}>
        <h3>My Plugin Result</h3>
        <div>
          <label>
            Buffer Size:
            <input
              type="number"
              value={this.state.sizeBuffer}
              onChange={(e) => this.setState({ sizeBuffer: e.target.value })}
            />
          </label>
          <select
            value={this.state.uniteBuffer}
            onChange={(e) => this.setState({ uniteBuffer: e.target.value })}
          >
            <option value="meters">Meters</option>
            <option value="kilometers">Kilometers</option>
          </select>
        </div>
        <p>Click on the map to create a buffer and query data.</p>
        {features.length > 0 && (
          <div>
            <h4>Query Results: {features.length} features found</h4>
            {/* Add Grid component here if needed */}
          </div>
        )}
      </div>
    );
  }
}

const mapStateToProps = (state) => ({
  features: selectFeatures(state),
});

const mapDispatchToProps = (dispatch) => ({
  setFeatures: (features) => dispatch(setFeatures(features)),
});

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(withLocalize(ResultComponent));
```


## 🎯 **Development Workflow and Best Practices**

### **The Plugin Development Lifecycle**

1. **Planning Phase**: Define what your plugin will do and what data it needs
2. **Setup Phase**: Create project structure and configuration files
3. **Development Phase**: Build components, implement logic, test functionality
4. **Integration Phase**: Connect with the main application and test with real data
5. **Deployment Phase**: Package and distribute your plugin

### **Best Practices for Professional Development**

#### 🏗️ **Architecture Principles**

**1. Single Responsibility Principle**
- Each component should do **one thing well**
- MainComponent: Controls when things happen
- ResultComponent: Handles user interface and interactions
- Services: Handle external data communication

**2. State Management Strategy**
- **Redux for shared data**: Information that multiple components need (query results, loading states)
- **Local state for UI**: Component-specific data (form inputs, temporary selections)
- **Props for configuration**: Settings passed down from parent components

**3. Error Handling Philosophy**
```javascript
// Always anticipate what could go wrong
try {
  const result = await riskyOperation();
  // Handle success
} catch (error) {
  console.error('Specific error context:', error);
  // Show user-friendly message
  // Provide fallback behavior
}
```

#### 🧹 **Resource Management**

**Why cleanup matters**: Map applications create visual elements and event listeners that consume memory. Without proper cleanup, your plugin could slow down or crash the application.

```javascript
componentWillUnmount() {
  // Always clean up what you created
  if (this.drawing) {
    this.props.removeInteraction(this.drawing);
  }
  if (this.vectorLayer) {
    this.props.removeVectorLayer(this.vectorLayer);
  }
}
```

#### 🌍 **Internationalization (i18n)**

**Why it matters**: Your plugin might be used by people who speak different languages.

```javascript
// Instead of hardcoded text:
<button>Click to search</button>

// Use localization:
<button>{t('search.button')}</button>
```

### **Common Development Patterns Explained**

#### 🗺️ **Map Interaction Pattern - The Foundation**

**What it does**: Creates tools that let users draw on the map

**When to use**: Any time you need user input on the map (clicking, drawing shapes, selecting areas)

```javascript
// The pattern: Get APIs → Create Layer → Create Tool → Set Callbacks
const [Drawing, VectorLayer] = await apiRegistry.getApis(["Drawing", "VectorLayer"]);
this.vectorLayer = new VectorLayer();  // Where drawings will be stored
this.drawing = new Drawing({           // The drawing tool
  type: "point",                      // What type of drawing (point, line, polygon)
  vectorLayer: this.vectorLayer       // Where to put the results
});
this.drawing.setOnDrawFinish(this.handleDrawFinish); // What to do when user finishes drawing
```

#### 🔗 **Component Communication Pattern - Plugin Orchestration**

**What it does**: Allows different parts of your plugin to show and hide each other

**When to use**: When one component needs to display another (like showing results after a search)

```javascript
// Show a component (like opening a dialog or panel)
this.props.showComponent(
  "my-plugin",           // Which plugin owns the component
  "ResultComponent",     // Which component to show
  { data: searchResults }, // Data to pass to the component
  (componentId) => {     // Callback when component is created
    this.resultComponentId = componentId; // Save ID for later cleanup
  }
);

// Remove a component (like closing a dialog)
this.props.removeComponent(this.resultComponentId);
```

#### 🔍 **Spatial Query Pattern - The Heart of GIS**

**What it does**: Finds geographic features that meet certain spatial criteria

**When to use**: Searching for "what's near this point" or "what's inside this area"

```javascript
// The pattern: Define what you're looking for → Where to look → How they should relate
const queryBody = [{
  dataSource: { 
    id: layer.id  // Which map layer to search in
  },
  filter: {
    conditionList: [{
      spatialCondition: {
        key: layer.geometryField.fieldName,  // The geometry column name
        geometry: JSON.stringify(searchArea), // The area to search within
        spatialRelation: "INTERSECT"          // How features should relate (intersect, contain, etc.)
      }
    }],
    logicalOperation: "AND"  // How to combine multiple conditions
  },
  crs: layer.crs  // Coordinate reference system
}];
```

### **🚀 Getting Started Checklist**

- [ ] Understand the problem your plugin will solve
- [ ] Set up project structure with proper folders
- [ ] Create package.json with correct dependencies
- [ ] Configure Babel for JavaScript transpilation
- [ ] Create test data files for development
- [ ] Build main component with proper lifecycle management
- [ ] Implement Redux for state management
- [ ] Add proper error handling and cleanup
- [ ] Test with real data and edge cases
- [ ] Document your plugin's configuration options

### **🎓 Learning Path for Junior Developers**

1. **Start Simple**: Begin with a plugin that just displays "Hello World"
2. **Add Interaction**: Make it respond to map clicks
3. **Add State**: Store and display some data
4. **Add Services**: Connect to external APIs
5. **Add Polish**: Error handling, loading states, user feedback
6. **Add Configuration**: Make it customizable for different use cases

Remember: **Every expert was once a beginner**. Focus on understanding the concepts first, then worry about optimization and advanced features. The most important skill in plugin development is breaking complex problems into smaller, manageable pieces.
