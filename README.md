# ZYLLIO Plugin Development guide

Zyllio SDK is a development kit that allows developers to extend Zyllio Studio by creating Plugins. These plugins contain Components, Actions, Formula functions and Themes

- Components are Visual Components set in screens like Texts, Lists, Buttons, ...
- Actions are pieces of logic executed when the mobile app. user presses a button
- Formula functions are piece of presentation to display complex data (math, substring, unit convertion, distance calculation...)
- Themes are shade of colors to customize the appearance of screens across the whole mobile app

It is the decision of developer to define the granularity of a plugin, it may contain any number of Components, Actions, Functions and Themes altogether 

# Experiment

Use these pre-built plugins to experiment Zyllio Plugins within Zyllio Studio

| Plugin | Plugin URL |
| ------- | --- |
| Progress Bar Component | https://zyllio.github.io/zyllio-plugin-progressbar/dist/plugin.js |
| Ionic Slider Component | https://zyllio.github.io/zyllio-plugin-ionic-slider/dist/plugin.js | 
| Rating Component | https://zyllio.github.io/zyllio-plugin-rating/src/rating.js |
| Angular Timeline Component | https://zyllio.github.io/zyllio-plugin-angular/dist/plugin.js |
| ReactJS Chart Component | https://zyllio.github.io/zyllio-plugin-reactjs/dist/plugin.js | 
| VueJS Counter Component | https://zyllio.github.io/zyllio-plugin-vuejs/dist/js/app.js | 
| Meme Generator Action | https://zyllio.github.io/zyllio-plugin-meme-generator/src/meme.js | 
| Custom Theme | https://zyllio.github.io/zyllio-plugin-theme/src/theme.js | 


# Plugin repositories

| Plugin | GitHub Repository |
| ------- | --- |
| Progress Bar Component | https://github.com/zyllio/zyllio-plugin-progressbar |
| Ionic Slider Component | https://github.com/zyllio/zyllio-plugin-ionic-slider | 
| Rating Component | https://github.com/zyllio/zyllio-plugin-rating |
| Angular Timeline Component | https://github.com/zyllio/zyllio-plugin-angular |
| ReactJS Chart Component | https://github.com/zyllio/zyllio-plugin-reactjs | 
| VueJS Counter Component | https://github.com/zyllio/zyllio-plugin-rating | 
| Meme Generator Action | https://github.com/zyllio/zyllio-plugin-meme-generator | 
| Custom Theme | https://github.com/zyllio/zyllio-plugin-theme | 

# Development environment

Zyllio SDK requires Node.js 16+ installed on any operating system it runs

Zyllio team recommends development tools that support Javascript, Typescript technical stack

These following tools are recommended: <a href="https://code.visualstudio.com/" target="_blank">VS Code</a> and <a href="https://www.jetbrains.com/" target="_blank">WebStorm</a> 

## Install Zyllio SDK

To install Zyllio SDK run the folowing command
```Shell
npm install @zyllio/zy-sdk --save-dev
```

## Use Zyllio SDK

Zyllio SDK is a Typescript Library meant to be consumed from a Typescript project. Using Typescript is the recommended approach even though it is not mandatory and could be used from pure Javascript projects

To use Zyllio SDK, add this statement at the top of your index file

``` 
/// <reference types="@zyllio/zy-sdk" />
```

This statement allows the development tool to discover the services exposed by Zyllio SDK and thus provide completion and inline documentation

# Reference API

All services are available from the `zySdk.services` namespace. For instance, dictionary service is accessed through this namespace: `zySdk.services.dictionary`

>This document is an overview of the reference API, please consider the comprehensive Reference API for more information <a href="https://zyllio.github.io/zyllio-sdk/" target="_blank">Zyllio SDK Reference API</a> 

## Registering Components

Zyllio Components have to implement a CustomElement from [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components) standard. Any Javascript frameworks could be used to develop this custom element: Pure JS, Angular, Vue.Js, StencilJS, ReactJS (using react-to-webcomponent)...

Please refer to GitHub examples to review components made with different technologies

Here is fairly simple example based on Pure Javascript

```typescript 
class MyComponent extends HTMLElement {
  ...
}
``` 

In addition, a Metadata is required to describe the component to Zyllio platform, see below for more details. To register a component and its metadata use the following API assuming MyComponentMetadata is a JSON object and MyComponent a custom element Class

```typescript
zySdk.services.registry.registerComponent(MyComponentMetadata, MyComponent)
```

## Component Metadata

Component Metadata is a static Javascript object that describes the component, it is required by Zyllio Studio to display its properties and styles from Design Editor. 

Click here to see component configuration panels
- [Properties Panel](./images/properties.png)
- [Styles Panel](./images/styles.png)

This metadata is compliant to these specifications

```typescript
const Icon = `
  <svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="24" height="24" viewBox="0 0 24 24">
    <path fill="#cccccc" d="M12,2A10,10 0 0,0 2,12A10,10 0 0,0 12,22A10,10 0 0,0 22,12A10,10 0 0,0 12,2Z" />
  </svg>
`

const MyComponentMetadata = {
  metadataVersion: 2, /* Must be version 2 */
  id: 'zyllio-list', /* Unique identifier, must be lower case and contain a dash character */  
  icon: Icon, /* SVG icon displayed in Zyllio Studio  */
  label: 'List', /* Label displayed in Zyllio Studio  */
  category: 'Basics', /* Category in which the component is displayed in Zyllio Studio */
  subCategory: 'Lists', /* Sub category in which the component is displayed in Zyllio Studio */
  hidden: false, /* Should be false */
  properties: [{ /* Properties to configure the component */
    id: 'name', /* Unique id of the property, should be lower case */
    name: 'Name', /* Name of the property displayed in Zyllio Studio  */
    type: 'row-variable', /* Type of the property (see Reference API) */
    options: [], /* Array of possible options, used only when type is PropertyTypes.Options */
    tootip: '', /* Tooltip displayed in Zyllio Studio  */
    default: '', /* Default value if any, it is assigned at component creation by Zyllio Studio user */
    write: true, /* Indicates whether this property is used to save data at runtime (likely when a component allows selections or inputs) */
    main: true /* Indicates whether this property is the main one, Zyllio Studio needs it to populate the component panel. One main property must be defined */
  }],
  styles: [{ /* Styles to configure the component (width and height styles are mandatory) */
    id: 'width', /* Unique id of the style, should be lower case */
    name: 'Width', /* Name of the style displayed in Zyllio Studio */
    type: 'width', /* Any of the folowing: width, height, background-image, background-color, icon, size, font-size, color, font-weight,  font-style, border-width, border-color, border-radius, box-shadow */
    default: '360px' /* Default value if any, it is assigned at component creation by Zyllio Studio user */
  }]
```

## Registering Actions

An action is a piece of logic executed by the mobile app. most of the time when a component is pressed

An Action is a Typescript class that implements `ActionInterface` and implements an `execute` method as per this example

```typescript
class MyAction implements ActionInterface {
  async execute(properties: PropertyModel[]) {
    ...
  }
}

```

To register an action to Zyllio platform, use `zySdk.services.registry` service 

```typescript
const myActionInstance = new MyAction()

zySdk.services.registry.registerAction(MyActionMetadata, myActionInstance)
```

## Action Metadata

Action Metadata is a static Javascript object that describes the action, it is required by Zyllio Studio to display its properties from the Action Editor. This metadata is compliant to these specifications

```typescript
const Icon = `
  <svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="24" height="24" viewBox="0 0 24 24">
    <path fill="#cccccc" d="M12,2A10,10 0 0,0 2,12A10,10 0 0,0 12,22A10,10 0 0,0 22,12A10,10 0 0,0 12,2Z" />
  </svg>
`

const MyActionMetadata = {
  id: 'get-random-meme', /* Unique identifier, must be lower case */
  icon: Icon, /* SVG icon displayed in Zyllio Studio */
  label: 'Get random meme', /* Label displayed in Zyllio Studio */
  category: 'Memes', /* Category in which the action is displayed in Zyllio Studio  */
  properties: [{ /* Properties to configure the action */
    id: 'value', /* Unique id of the property, should be lower case */
    name: 'Meme URL', /* Label displayed in Zyllio Studio  */
    type: 'row-variable', /* Type of the property from PropertyTypes enum (see Reference API) */
    options: [], /* Array of possible options, used only when type is PropertyTypes.Options */
    tootip: `The meme image`, /* Tooltip displayed in Zyllio Studio */
    default: '', /* Default value if any, it is assigned at component creation by Zyllio Studio user */
    main: true, /* Indicates whether this property is the main one */
    write: true /* Indicates whether this property is used to save data at runtime (likely when a component allows selections or inputs) */
  }]
}
```

## Registering Formula functions

Formula functions are piece of presentation to display complex data (math, substring, unit convertion, distance calculation)

A Function is a Typescript class that implements `FunctionInterface` and implement an `execute` method as per this example

```typescript
class MyFunction implements FunctionInterface {
  execute(properties: PropertyModel[]) {
    ...
  }
}

```

To register a Formula function to Zyllio platform, use `zySdk.services.registry` service 

```typescript
const myFunctionInstance = new Myfunction()

zySdk.services.registry.registerFunction(MyFunctionMetadata, myFunctionInstance)
```

## Formula Function Metadata

Function Metadata is a static Javascript object that describes the function, it is required by Zyllio Studio to display its properties from the Formula Editor. This metadata is compliant to these specifications

```typescript
const IconData = `
  <svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="24" height="24" viewBox="0 0 24 24" fill="#cccccc">
    <path d="M9,10H7V12H9V10M13,10H11V12H13V10M17,10H15V12H17V10M19,3H18V1H16V3H8V1H6V3H5C3.89,3 3,3.9 3,5V19A2,2 0 0,0 5,21H19A2,2 0 0,0 21,19V5A2,2 0 0,0 19,3M19,19H5V8H19V19Z" />
  </svg>
`

const DateTimeMetadata = {
  id: 'date', /* Unique identifier, must be lower case */
  icon: IconData, /* SVG icon displayed in Zyllio Studio */
  label: 'New date & time', /* Label displayed in Zyllio Studio */
  category: 'Date', /* Category in which the function is displayed in Zyllio Studio  */
  properties: [{ /* Properties to configure the function */
    id: 'date', /* Unique id of the property, should be lower case */
    name: 'Date', /* Label displayed in Zyllio Studio  */
    type: 'date', /* Type of the property (see Reference API) */
    tootip: `The date displayed`, /* Tooltip displayed in Zyllio Studio */
    default: '', /* Default value if any, it is assigned at component creation by Zyllio Studio user */
    main: true, /* Indicates whether this property is the main one */
  }]
}
```

## Registering Themes

Themes are shade of colors to customize the appearance of screens across the whole mobile app

A theme is defined as per these specifications

```typescript
const theme = {
  name: 'MyTheme', /* Name displayed in Zyllio Studio  */
  theme: {
    primaryColor: '#4f7d96', /* Background color used in primary components (header, footer) */
    primaryTextColor: '#ffffff', /* Text color used in primary components (header, footer) */
    secondaryColor: '#fe844b', /* Background color used in secondary components (button, list, carousel) */
    secondaryTextColor: '#ffffff', /* Text color used in secondary components (button, list, carousel) */
    tertiaryColor: '#0000000a', /* Background color used in tertiary components (input fields) */  
    tertiaryTextColor: '#000000', /* Text color used in tertiary components (input fields) */
    backgroundColor: '#f0f6f9', /* Screen background color */
    textColor: '#474f5b', /* Text color */
    rtl: false /* Indicates whether Right To Left alignment should be activated mainly to support Arabic and Asian languages */
  }
}
```
To register a new Theme, use `zySdk.services.registry` service 

```typescript
zySdk.services.registry.registerTheme(theme)
```

## Use theme from Visual components

Visual components could make use of theme style properties using the following CSS variables

These variables are set at body level automatically by Zyllio SDK at startup

- --theme-primary-color : Background color used in primary components (header, footer) 
- --theme-primary-text-color: Text color used in primary components (header, footer) 
- --theme-secondary-color: Background color used in secondary components (button, list, carousel)
- --theme-secondary-text-color: Text color used in secondary components (button, list, carousel) 
- --theme-tertiary-color: Background color used in tertiary components (input fields) 
- --theme-tertiary-text-color: Text color used in tertiary components (input fields) 
- --theme-background-color: Screen background color
- --theme-text-color: Text color
- --direction: Indicates whether Right To Left alignment should be activated to support Arabic and Asian languages



## Access application runtime 

Component and Actions may need to interact with application runtime using `zySdk.services.runtime` service 

### Trigger screen transition

```typescript
zySdk.services.runtime.navigate(screen)
```

### Reset application 

```typescript
zySdk.services.runtime.reset()
```

### Navigate to specific screen 

```typescript
zySdk.services.runtime.navigate(screen)
```

### Navigate back 

```typescript
zySdk.services.runtime.back()
```

### Get current screen

```typescript
const screenId = zySdk.services.runtime.getCurrentScreenId()
```

### Trigger transition

Triggers the flow transition defined by the Zyllio Studio user  

```typescript
zySdk.services.runtime.triggerTransition(element)
```

## Access property values

A component or an action may need to access the property values. For instance, a component may need to get the Table assigned by the Zyllio Studio user

- Component: use `zySdk.services.component` to retrieve property values
- Actions: use `properties` argument passed to execute method

```typescript
// Action use case
const propertyValue = properties.find(p => p.id === 'table')

// Component use case
const propertyValue = zySdk.services.component.getPropertyValue(this, 'table')

// Cast the property accordingly, here it is a Table property value
const tableId = (propertyValue as TablePropertyValueModel).tableId

```

## Access data dictionary

A mobile appliction uses data to hold user selections accross the screen flow. For example, a product item could be selected or a phone number could be enterered

Accessing data is supported by 2 services

- `zySdk.services.component` to retrieve property values
- `zySdk.services.dictionary` to read / write / monitor data dictionary

### Read data dictionary

```typescript
// Action use case
const propertyValue = properties.find(p => p.id === 'value')

// Component use case
const propertyValue = zySdk.services.component.getPropertyValue(this, 'value')

const value = zySdk.services.dictionary.getValue(propertyValue)
```

### Write data dictionary

```typescript
// Action use case
const propertyValue = properties.find(p => p.id === 'value')

// Component use case
const propertyValue = zySdk.services.component.getPropertyValue(this, 'value')

zySdk.services.dictionary.setValue(propertyValue, newValue)
```

### Monitor data dictionary changes

The intent is to get a notification when a data has changed likely from another component or action

```typescript
// Action use case
const propertyValue = properties.find(p => p.id === 'value')

// Component use case
const propertyValue = zySdk.services.component.getPropertyValue(this, 'value')

zySdk.services.dictionary.onChange(propertyValue, () => {
  // Code that updates the component
})
```

## Access Zyllio database 

A component has access to Zyllio database, made of tables, through the `zySdk.services.storage` service. This is an overview, please refer to <a href="https://zyllio.github.io/zyllio-sdk/" target="_blank">Zyllio SDK Reference API</a> for more details

### To insert a data row 
```typescript
await zySdk.services.storage.createRow(tableId, data)
```

### To update a data row
```typescript
await zySdk.services.storage.updateRow(tableId, rowId, data)
```

### To retrieve data rows

Method 1 High level API : This convenient API supports tables, relations, filters and sorts

```typescript

const list: ListItemsModel = await zySdk.services.list.retrieveData(propertyValue)

```

Method 2 Low level API : This API returns raw data not filtered and sorted. Relation table needs to be solved before using it 

```typescript

const rows: TableRowsModel = zySdk.services.storage.retrieveRows(tableId)

```

### To delete a data row
```typescript     
await zySdk.services.storage.deleteRow(tableId, rowId)
```

## Display a messagebox

To inform a user, a component or action could display a message box using `zySdk.services.dialog` service

```typescript
zySdk.services.dialog.show({
  text: `Field Email is invalid`,
  title: 'Error'
})
```

## Display a loading icon 

To inform a user a long process is happening like retrieving remote data, a component or an action could use `zySdk.services.loading` service 

```typescript
zySdk.services.loading.show()

// Doing long process

zySdk.services.loading.hide()
```

# Support

Zyllio team is available for any support, feature requests and questions

- email: contact@zyllio.com
- twitter: [@zyllio](https://twitter.com/zyllio)


