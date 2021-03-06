---
Order: 2
Area: containers
TOCTitle: Node.js
ContentId: A963901F-BF3F-455F-AD75-AB54EAE72BEF
PageTitle: Build and run a Node.js app in a container
DateApproved: 01/29/2020
MetaDescription: Develop, build, and debug a Node.js app in a Docker container, using Visual Studio Code.
---
# Node.js in a container

In this guide you will learn how to:

- Create a `Dockerfile` file for an [Express](https://expressjs.com/) Node.js service container
- Build, run, and verify the functionality of the service
- Debug the service running within a container

## Prerequisites

- Both Docker and the VS Code Docker extension must be installed as described in the [overview](overview.md#installation)
- [Node.js](https://nodejs.org/) version 10 or later

## Create an Express Node.js application

1. Create a folder for the project.
1. Open a development command prompt in the project folder and create the project:

   ```bash
   npx express-generator
   npm install
   ```

## Add Docker files to the project

1. Open the project folder in VS Code.
1. Open the Command Palette (`kb(workbench.action.showCommands)`) and use **Docker: Add Docker Files to Workspace...** command:

   ![Add Dockerfile to a Node.js project](images/quickstarts/node-add-node.png)

1. Select **Node.js** when prompted for the application platform.
1. Select either **Yes** or **No** when prompted to include Docker Compose files.

   > The Docker Compose files are optional and not used for debugging the application within a container, so either is a valid choice.

1. Enter `3000` when prompted for the application port.

The extension will create `Dockerfile` and `.dockerignore` files. If you elected to include Docker Compose files, `docker-compose.yml` and `docker-compose.debug.yml` will be generated as well. Finally, the extension will create a set of **VS Code tasks** in `.vscode/tasks.json` for building and running the container (in both debug- and release-configurations) and a **launch debug configuration** in `.vscode/launch.json` for debugging the service within the container.

## Run the service locally

1. Open a terminal (`kb(workbench.action.terminal.toggleTerminal)`).
1. Enter `npm run start` to start the application:

   ```output
   > express-app@0.0.0 start /Users/user/code/scratch/express-app
   > node ./bin/www
   ```

1. Open the web browser and navigate to [http://localhost:3000](http://localhost:3000). You should see a page similar to the following:

   ![Application page in browser](images/quickstarts/node-run-browser.png)

1. When done testing, type `kbstyle(Ctrl+C)` in the terminal.

## Build the service image

1. Open the Command Palette (`kb(workbench.action.showCommands)`) and select the **Docker Images: Build Image...** command.
1. Open the Docker view and verify that the new image is visible in the Images tree:

   ![Verify Docker image exists](images/quickstarts/node-verify-image.png)

## Run the service container

1. Right-click on the image built in the previous section and select **Run** or **Run Interactive**. The container should start and you should be able to see it in the Docker Containers tree:

   ![Running service container](images/quickstarts/node-running-container.png)

1. Open the web browser and navigate to [http://localhost:3000](http://localhost:3000). You should see a page similar to the following:

   ![Application page in browser](images/quickstarts/node-run-browser.png)

1. When done testing, right-click the container in the Containers tree and select **Stop**.

## Debug in the service container

When the Docker extension adds files to the application, it also adds a **VS Code debugger configuration** in `.vscode/launch.json` for debugging the service when running inside a container. The extension detects the protocol and port used by the service and points the browser to the service, but make sure the application's output is written to the debug console.

1. Open the `.bin/www` file in the editor.
1. After the `require()` statements, add a line that forces the debug logger to write to the debug console, as shown below:

   ```javascript
   var app = require('../app');
   var debug = require('debug')('express-app:server');
   var http = require('http');

   // Add the following line to force the debug logger to write to the debug console.
   debug.log = console.debug.bind(console);
   ```

1. Set a breakpoint in the `get()` handler for the `'/'` route in `routes/index.js`.

1. Make sure the **Docker Node.js Launch and Attach** debugger configuration is selected.

   ![Selected Docker debug configuration](images/quickstarts/node-debug-configuration.png)

1. Start debugging (use the `kb(workbench.action.debug.start)` key).
    - The Docker image for the service builds.
    - The Docker container for the service runs.
    - The browser opens to the (random) port mapped to the service container.
    - The debugger hits the breakpoint in `index.js`.

    > Note that, because the debugger attaches *after* the application starts, the breakpoint may missed the first time around; you might have to refresh the browser to see the debugger break on the second try.
    >
    > You can configure the application to wait for the debugger to attach before starting execution by setting the [inspectMode](/docs/containers/reference.md#node-object-properties-docker-run-task) property to `break` in the `docker-run: debug` task in `tasks.json`.

## Next steps

[Learn more about debugging Node.js](/docs/containers/debug-node.md)
