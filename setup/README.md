<h1>
  <span class="headline">Building a Microservice with Express JS</span>
  <span class="subhead">Setup</span>
</h1>

## Setup

In this setup lesson, you will prepare your environment for building a microservice with Express JS. Follow the steps below to ensure your development workspace is configured properly.

### 1. Create the project directory

Start by creating a new directory on your workstation to house your microservice project.

Open your terminal and navigate to the `Desktop`:

```bash
cd ~/Desktop
```

Create a directory called `accounts-service` and navigate into it:

```bash
mkdir accounts-service
cd accounts-service
```

Open the project in **VS Code** to begin coding:

```bash
code .
```

### 2. Initialize the project

To set up your project structure, you will need to create a `package.json` file. This file will manage your project’s metadata and dependencies.

Run the following command to create the `package.json` file with default settings:

```bash
npm init
```

> This will create a `package.json` file in the root of your project.

#### What is `package.json`?

The `package.json` file is essential for any `Node.js` project, including your Express app. It manages project details, dependencies, and configurations. It allows you to define your app’s entry point, specify installed packages, and add custom scripts for running your application (ex: starting the server with npm start). This file ensures your development environment is consistent and makes it easy to share and deploy your project.

### 3. Install required packages

To build our Express app and add the necessary functionality, we'll install the following packages:

- [express](https://www.npmjs.com/package/express): The core framework to build the API.
- [@aws-sdk/client-dynamodb](https://www.npmjs.com/package/@aws-sdk/client-dynamodb): The AWS SDK for interacting with DynamoDB.
- [@aws-sdk/lib-dynamodb](https://www.npmjs.com/package/@aws-sdk/lib-dynamodb): A library to simplify DynamoDB interactions.
- [dotenv](https://www.npmjs.com/package/dotenv): A package to manage environment variables.
- [joi](https://www.npmjs.com/package/joi): A validation library for defining schemas and validating data.
- [cors](https://www.npmjs.com/package/cors): A middleware to enable cross-origin resource sharing.
- [morgan](https://www.npmjs.com/package/morgan): A logging tool for monitoring incoming requests.

Run the command below to install all the required packages:

```bash
npm install express @aws-sdk/client-dynamodb @aws-sdk/lib-dynamodb dotenv joi cors morgan
```

After this step, the `package-lock.json` file and `node_modules` folder will be created automatically.

### 4. Configure the `package.json` file

To enable ES module support in Node.js, we need to modify the `package.json` file.

Add the line: `"type": "module",` at the top level of the `JSON` object.

Your `package.json` should now look similar to this:

```json
{
  "name": "accounts-service",
  "version": "1.0.0",
  "description": "A microservice for managing accounts",
  "main": "index.js",
  "type": "module",    <--- Add here!
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.17.1",
    "@aws-sdk/client-dynamodb": "^3.x.x",
    "@aws-sdk/lib-dynamodb": "^3.x.x",
    "dotenv": "^10.x.x",
    "joi": "^17.x.x",
    "cors": "^2.x.x",
    "morgan": "^1.x.x"
  }
}
```

### 5. Create the entry point file

To define the main file that will start your Express app, you need to create an `index.js` file. This file serves as the entry point for your application.

Create the `index.js` file:

```bash
touch index.js
```

### Next Steps

With the setup complete, you are now ready to start building your Express microservice. In the next lesson, we will build the foundational structure of the app, set up routes, and integrate it with DynamoDB.
