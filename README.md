# Connectors

## What is a connector?

A connector is a service that can extend the Mavenlink Workflow Platform with custom triggers and actions. 

Triggers and actions are the basic building blocks of Workflows.

Triggers check for new events and return the list of new events to the platform. The platform will poll the connector at regular intervals (currently every 5 seconds) to check for new events. The Platform will use the list of events to decide which Workflows to run.

Actions take a list of inputs, execute some custom business logic, and return a list of outputs to the platform.

## Why create a connector?

By chaining a trigger and multiple actions users can create custom workflows, or append new behavior to existing workflows. The Core Library will provide many common triggers and actions, but could never exhaust every possibility. Connectors allow people with coding knowledge to extend the library by creating new triggers and actions.

## How does a connector work?

The connector service can be implemented in any language that has support for [gRPC](https://grpc.io/docs/languages/). gRPC is a framework for making Remote Procedure Calls and enables the communication interface between the Workflow Platform and your connector.

To be a valid connector and interface with the platform, a connector is responsible for implementing four behaviors:

1. Listing all available triggers.
2. Listing all available actions.
3. Performing a specified trigger.
4. Performing a specified action.

The API for all 4 of these behaviors is defined by the `Connector` service found in the `connector.proto` file in this repository. This file can be used to generate your gRPC server.

---

# Creating a Connector

To create a connector:

1. [Generate the gRPC code](#generating-a-connector)
2. [Write your custom code](#writing-your-custom-code)
3. [Host your Connector](#hosting-your-connector)
4. [Register your Connector with the Platform](#registering-your-connector)

## Generating a Connector

The process to generate a connector will vary depending on the programming language you choose. The instructions for what generation tool to use can be found in the quick start guides in the [official gRPC documentation for your language](https://grpc.io/docs/languages/). You will need to use the specific generator for your language.

## Writing Your Custom Code

The generated gRPC code will provide stubs for the four connector behaviors:

1. `triggers` - Called by the Platform on registration or refresh to provide a list of available triggers. Returns a list of triggers. The list may be empty if the connector does not implement any triggers.
2. `actions` - Called by the Platform on registration or refresh to provide a list of available actions. Returns a list of actions. The list may be empty if the connector does not implement any actions.
3. `perform_trigger` - Called by the platform periodically to poll for new events. Returns a list of new events. The list may be empty if there are no new events.
4. `perform_action` -  Called by the platform when this action is run by a workflow. The Platform will pass in the name of the action and the parameters. Your code is responsible for switching on the name of the action and calling the correct custom functions/methods using the provided parameters.

## Hosting Your Connector

Hosting a connector is a very context-driven decision depending on the language and your needs. However you choose to host it, your service needs to be accessible by HTTPS.

## Registering Your Connector

Once your connector is hosted and available, you may register your connector with the Platform. Go to your Library, and click the button labeled "Register Connector." Give it name and URL.

Once the connector is registered, the Platform will automatically add the new triggers and actions to your Library to be used by Workflows.

Your triggers and actions will be available to your account, and any account you share them with.

No other account will be able to view your connector, but shared triggers and actions will make gRPC calls to your connector.
