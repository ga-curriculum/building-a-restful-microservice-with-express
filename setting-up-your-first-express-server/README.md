<h1>
  <span class="headline">Building a Microservice with Express JS</span>
  <span class="subhead">Setting Up Your First Express Server</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to create and run a basic Express server to handle incoming HTTP requests.

### 1. Create the `index.js` file

To get started, create the entry point for your application by adding the following code to `index.js`:

```js
import express from 'express';

const port = 3000;
const app = express();

app.use(express.json());

async function startServer() {
  try {
    app.listen(port, () => console.log(`🤖 Listening on Port: ${port}`));
  } catch (err) {
    console.log('🤖 Oh no, something went wrong', err);
  }
}

startServer();
```

To build a microservice, it’s important to understand the main components that make up an Express server. Let's break down each part of the `index.js` code step-by-step:

- **`import express from "express";`**: Imports the Express library.

- **`const port = 3000;`**: This line declares a variable called `port` and assigns it the value `3000`.

- **`const app = express();`**: Creates an instance of an Express application.

- **`app.use(express.json());`**: Adds middleware to parse JSON requests.

- **`app.listen(port, ...)`**: Starts the server on the specified port and logs when it’s ready.

### 2. Run the app locally

Run your application using the command below in your terminal:

```bash
node index.js
```

This command uses the local installation of Node.js to execute your script. Once the server is running, you should see a message like:

```bash
🤖 Listening on Port: 3000
```

### 3. Stop the server

To stop the server, use `ctrl + c` in your terminal. This will terminate the running process and stop the server from listening on the specified port.

> **Note:** If you see any errors, check the code for typos or missing imports. This exercise sets the groundwork for understanding how an Express server is created and run, which is essential for building more complex APIs and microservices.
