<h1>
  <span class="headline">Building a RESTful Microservice with Express</span>
  <span class="subhead">Containerizing the Express API</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to containerize an Express application, manage environment variables effectively, and set up a Docker Compose workflow.

In this lesson, we will take our existing Express API and prepare it for deployment in a cloud environment.

To do this, we will need to:

- **Containerize the application using Docker:** This allows our application to run consistently across different environments, reducing deployment issues.

- **Use environment variables for configuration:** These make the application flexible and easier to adapt to different environments without changing the code.

- **Use Docker Compose to follow real time local changes in the container:** This tool streamlines managing and running multi-container applications, saving time and effort during development.

## Containerizing the application

Let's write a Dockerfile that will turn our app into an image, which we can use to run a container with our app inside.

To begin, create a `Dockerfile` in the root of your project. This file defines how our app will be built and run inside a container.

```sh
touch Dockerfile
```

### 1. Add the following content to your `Dockerfile`:

```Dockerfile
FROM node:18-alpine

# Take environment variables
ARG NODE_ENV=prod
ARG PORT=8080
ENV NODE_ENV=$NODE_ENV
ENV PORT=$PORT

# Create app directory
WORKDIR /usr/src/app

COPY package*.json ./

RUN if [ "$NODE_ENV" = "dev" ]; then \
    npm install; \
    else \
    npm install --omit=dev; \
    fi

# Copy app source code
COPY ./ ./

# Expose port and start application
EXPOSE $PORT
CMD npm run $NODE_ENV --loglevel=verbose
```

## Building the Docker image

Run the following command in your terminal to build the Docker image. Replace `your-image-name` with a name for your image:

```sh
docker build -t your-image-name .
```

### Running the Docker container

After the image is built, run the container using the following command, replacing `your-image-name` with the name you used in the build step:

```sh
docker run -p 8080:8080 your-image-name
```

You can also pass custom environment variables using the `-e` flag:

```sh
docker run -p 8080:8080 -e NODE_ENV=dev -e PORT=8080 your-image-name
```

## Managing environment variables

Environment variables play a key role in making applications flexible and adaptable to different settings. By adding them to the `Dockerfile`, we can configure important details like `PORT` and `NODE_ENV` without hardcoding values into our source code. This makes it easy to run the app in different environments like development, testing, or production.

Now that the variables are defined in the `Dockerfile`, the next step is to use the `dotenv` package to access and manage these variables directly in our application code.

### 1. Update the `index.js` server file

```js
import dotenv from 'dotenv'; // Add the package here
dotenv.config(); // load config here

import express from 'express';
import Router from './views/router.js';

const port = process.env.PORT;
const app = express();

app.use(express.json());
app.use(Router);

async function startServer() {
  try {
    app.listen(port, () => console.log(`🤖 Listening on Port: ${port}`));
  } catch (err) {
    console.log('🤖 Oh no something went wrong', err);
  }
}

startServer();
```

### 2. Now let's create a `.env` file in the root of our project where we can define our environment variables.

```sh
touch .env
```

Add the following content to the `.env` file:

```sh
PORT=3000
NODE_ENV=dev
```

### 3. Build and run the container.

```sh
docker build -t your-image-name .
```

followed by:

```sh
docker run -p 3000:3000 your-image-name
```

Now, you can test your API in Postman or another tool by sending requests to http://localhost:3000/accounts.

> Note: Changing the port number in the docker run command will make the app run on a different port.

Congratulations! Your containerized application should now be running smoothly. Next, we can continue building out our API. However, there's one thing to keep in mind: just like before we used nodemon and had to restart the app for every change, we now need to rebuild the container each time we make updates.

## Improving development workflow with Docker Compose

Manually rebuilding the container for every change can be time-consuming and disrupt your workflow. Docker Compose addresses this challenge by simplifying container management, allowing you to configure and run multiple services with a single command. For microservices, this approach keeps everything organized and ensures updates and testing are seamless. Docker Compose helps make the development process faster, more streamlined, and efficient.

### 1. Install Docker Compose with the following commands:

```sh
sudo curl -SL https://github.com/docker/compose/releases/download/v2.11.2/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose &&
sudo usermod -aG docker $USER &&
sudo chgrp docker /usr/local/bin/docker-compose &&
sudo chmod 750 /usr/local/bin/docker-compose &&
```

### 2. Create a `docker-compose.yml` file in the root of the project.

```bash
touch docker-compose.yml
```

Add the following content to this file:

```yml
version: '3.8'

services:
  app:
    container_name: app
    restart: always
    build:
      context: .
      dockerfile: Dockerfile
      args:
        - NODE_ENV=${NODE_ENV}
        - PORT=${PORT}
    ports:
      - '${PORT}:${PORT}'
    volumes:
      - ./:/usr/src/app
      - /usr/src/app/node_modules
    environment:
      - PORT=${PORT}
```

This configuration sets up your app as a service, connecting your local project to the container and allowing environment variables to be passed in dynamically. This makes it easier to manage and adapt your app for different environments without hardcoding settings. For microservices, where multiple containers often need to work together, this setup simplifies the process by ensuring each service can be configured and connected reliably.

### 3. Running the application with Docker Compose

Now start your application with the following command:

```sh
docker-compose up --build
```

With this setup, Docker Compose will mount your local directory into the container. Any changes you make to the code will be reflected in the container, and you can restart the container to see the changes without rebuilding the image manually. This is a much better developer experience!

_Now_ we are ready to continue building our API!
