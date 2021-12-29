# ZYLLIO SDK

Zyllio SDK is a development kit that allows developers to extend Zyllio Studio by creating  Plugins. These plugins define Components, Actions and Themes

It is the decision of developer to define the granularity of a plugin, it may contain any number of Components, Actions and Themes altogether 

## Development environment

Zyllio SDK requires Node.js 14+ installed on any operating system it runs

Zyllio team recommends development tools that support Javascript, Typescript technical stack

These following tools are recommended: <a href="https://code.visualstudio.com/" target="_blank">VS Code</a> and <a href="https://www.jetbrains.com/" target="_blank">WebStorm</a> 

## Install Zyllio SDK

To install Zyllio SDK run the command

`npm install @zyllio/zy-sdk -save-dev` 

## Use Zyllio SDK

Zyllio SDK is a Typescript Library meant to be consumed from a Typescript project. Using Typescript is the recommended approach even though it is not mandatory

To use Zyllio SDK, add this statement at the top of your index file

`/// <reference types="@zyllio/zy-sdk" />`

This statement allows the development tool to understand the services exposed by Zyllio SDK and thus provide completion and inline documentation

> Warning: Zyllio SDK does not need to be imported using ES6 Imports. If so, it would likely introduce instabilities and loss of data onmobile devices 

## Reference API

All services are available from the zySdk.services namespace. For instance, dictionary service is accessed through 

`zySdk.services.dictionary`

### Reference API for Components

UI Component should implment a WebComponent (customElement) with ShadowDOM

Register a component: Registry

Read and Write data at runtime, Monitor data changes : Dictionary

Read / Update / Create / Delete to/from Zyllio database: Storage

Read and Write properties: Factory

Display a message: Dialog

Show Loading icon : Loading

Deal with runtime: Runtime  


### Reference API for Actions

Action extend ActionInterface and implement the execute method

Read and Write data at runtime : Dictionary

Read / Update / Create / Delete to/from Zyllio database: Storage

Register an action: Registry

Display a message: Dialog

Show Loading icon : Loading

Deal with runtime: Runtime  


### Reference API for Themes

Register a theme : Registry


