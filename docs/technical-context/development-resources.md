---
layout: default
title: Development Resources
parent: Technical Context
---
# Development Resources
## Architecture Diagram
![](../assets/images/architecture_diagram.png)

## React-Redux Data Flow Diagram
![](../assets/images/data_flow.png)

## API Documentation
### Postman
[Postman](https://www.postman.com/postman/) is a REST client, API runner, and documentation tool. We use it 
to test out our API endpoints without the overhead of the frontend. You can explore some of the documented endpoints [here](https://documenter.getpostman.com/view/9553211/TWDUqdn1).

Setting collection up locally: 
* If you want to explore the endpoints locally, you must [download Postman](https://www.google.com/url?q=https%3A%2F%2Fwww.postman.com%2Fdownloads%2F&sa=D&sntz=1&usg=AFQjCNGNCmoiHMIEgBbM7x_gB1sxfChi3g) first. 
Once it's installed, you will need to import the collection as well as the local environment. The files used are located in the `<project root>/doc/postman` directory.
If you already have Postman installed, you can simply click the following:
 
    [![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/ec8033f0f806e39f166c)

At the time of writing, they include:
* Police Data Management.*.json
* Local.*.json
* CI.*.json
* Staging.*.json

## Testing
Unit Tests

A unit test is meant to verify that a piece of code (i.e. method, component, process) is functioning as expected. Unit tests are meant to test
these units independent of dependencies, so we utilize mocking frameworks like [Jest](https://jestjs.io/docs/en/getting-started) and [Nock](https://github.com/nock)
*  We split our unit test suites into the following categories: 
    * Client - unit tests for client using Jest, [Enzyme](https://enzymejs.github.io/enzyme/), and [React-Testing](https://testing-library.com/docs/react-testing-library/intro/) library
    * Server & Worker - unit tests for server using Jest, request tests with [SuperTest](https://www.google.com/url?q=https%3A%2F%2Fgithub.com%2Fvisionmedia%2Fsupertest&sa=D&sntz=1&usg=AFQjCNGUe1lAcWNPyBtnhD4TSFhiVjK3RA),
    handler tests with nock

    Some of these tests utilize Builders defined within `src/sharedTestHelpers/`, which are ... 
    
    Additionally, for some of these unit tests, we use [Jest snapshots](https://jestjs.io/docs/en/snapshot-testing) to verify that components or documents render as we expect
    
E2E Tests

End-to-end tests are meant to test the application from the end user's perspective by simulating a real user session
* We use [NightWatch](https://nightwatchjs.org/guide/using-nightwatch/writing-tests.html) to write our automated E2E tests
    * Sessions configured to run on Chrome browser

Integration Tests

An integration test verifies that all modules within a codebase work together as expected.
* Our integration test suite is defined as a [Collection Runner](https://learning.postman.com/docs/running-collections/intro-to-collection-runs/) 
in Postman (see [API Documentation](https://publicdataworks.github.io/pdm-docs/technical-context/development-resources.html#api-documentation))

Health Check

We have a basic health check endpoint to ensure that our server is working and available

Conditionally Run Tests

You might notice that some test suites have messages likes "Skipping test...", this is because we have added flags to our application which
disable Cloud Services. The benefit of this is that you do not have to have an AWS or Auth0 account to begin using the Police Data Manager (simply run a `docker-compose up app`!).
However, if you want to, for whatever reason, use authentication or AWS locally, then follow these simple instructions
(You will need a login under a tenant of Auth0 and an AWS account to use the app locally):

* Note: You cannot have a situation in which authentication is on and AWS is off or vice versa. Both authentication and AWS have to be on or both authentication and AWS have to be off.

1. Locate the docker-compose.yml file (police_data_manager/docker-compose.yml). In this file, you should see:

    ```
    services:
        app:
        ...
        environment:
        ...
        - USE_CLOUD_SERVICES=false
        - REACT_APP_USE_CLOUD_SERVICES=false`
    ```
    * Note: Again, authentication and AWS will be assumed offline as mentioned above

2. Now, you must change USE_CLOUD_SERVICES and REACT_APP_USE_CLOUD_SERVICES from false to true.

3. Repeat steps 1 and 2 but now for the worker and app-e2e  services (within the same file, below the app service). 
    * For E2E tests, you must also change variable disableAuthentication in the nightwatch.conf.js file (police_data_manager/e2e/nightwatch.conf.js) from true to false. Note: These changes will now invoke authentication when running yarn e2e locally.
5. Run up localhost in your terminal like you normally would (docker-compose up app) and the app should now need AWS and authentication credentials and you can move about the app freely.