<h1>
  <span class="headline">Building a Microservice with Express JS</span>
  <span class="subhead">Creating an Express Router</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to set up an Express router, create routes for handling HTTP GET and POST requests, and run their server in development mode with automatic reloading.

## 1. Create the router file

To organize our code, we'll create a new folder called `views` and a file called `router.js` within it.

1. In your terminal, run the following command to create the folder and file:

```sh
mkdir views
touch views/router.js
```

## 2. Set up the router with routes

Now, we will define the router and add routes to handle the `/accounts` endpoint, which will support both **GET** and **POST** HTTP methods.

1. Add the following code to `views/router.js`:

```js
import express from 'express';

// Create a new router instance
const Router = express.Router();

// Define routes for the /accounts endpoint
Router.route('/accounts')
  .get((req, res) => {
    res.send(
      '🤖 Accounts Route with GET method - this endpoint will get all of the accounts from the database'
    );
  })
  .post((req, res) => {
    res.send(
      '🤖 Accounts Route with POST method - this endpoint will create a new account in the database'
    );
  });

// Export the router so it can be used in other files
export default Router;
```

## 3. Integrate the router into the main server

To use the newly created router, we need to import it into `index.js` and tell the application to use it.

1. Update your `index.js` file with the following code:

```js
import express from 'express';
import Router from './views/router.js';

// Set the port for the server
const port = 3000;
// Create an instance of the Express app
const app = express();

// Middleware to parse JSON bodies
app.use(express.json());

// Use the router for handling routes
app.use(Router);

// Start the server
async function startServer() {
  try {
    app.listen(port, () => console.log(`🤖 Listening on Port: ${port}`));
  } catch (err) {
    console.log('🤖 Oh no, something went wrong', err);
  }
}

startServer();
```

## 4. Test the endpoints

To test your setup, use Postman or a similar tool to send GET and POST requests to `http://localhost:3000/accounts`. You should see the response messages that were defined in `router.js`.

## 5. Enable automatic reloading during development

To improve our development workflow, we'll use `nodemon`, which automatically restarts the server whenever changes are made to the source code.

1. Install `nodemon` as a development dependency by running:

```sh
npm install nodemon --save-dev
```

2. Update the `scripts` section in your `package.json` to include `nodemon`:

```json
"scripts": {
"test": "echo \"Error: no test specified\" && exit 1",
"dev": "nodemon index.js",
"prod": "node index.js"
},
```

## 6. Run the app in development mode

- Use the command `npm run dev` to start the server with `nodemon` in development mode. This will automatically reload the server whenever you save changes to your files.

- For production use, run the command `npm run prod`, which will start the server without `nodemon`.

### Next Steps 

Next we need to create an accounts controller file and refactor our router to make our code more organized and readable.