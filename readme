# Complete Tutorial: Containerized Applications with Docker on Windows 11

In this tutorial, we'll walk through setting up three projects on your Windows 11 PC using Visual Studio Code (VS Code), Docker, Node.js, React.js, and Electron. Each project will be containerized to ensure all dependencies are encapsulated and portable.

## Prerequisites

Before starting, make sure you have the following installed on your Windows 11 PC:

1. **Visual Studio Code**: Download and install from [here](https://code.visualstudio.com/).
2. **Docker Desktop for Windows**: Install Docker Desktop from [here](https://www.docker.com/products/docker-desktop).
3. **Node.js**: Install Node.js from [here](https://nodejs.org/).
4. **MySQL Workbench**: Optional for managing MySQL databases; download from [here](https://www.mysql.com/products/workbench/).

## Step 1: Set Up the Containerized Web Server in Node.js with MySQL and React.js

### Initialize the Backend Server Project

1. **Create a new directory** for your backend server project and initialize a new Node.js project.

    ```sh
    mkdir backend-server
    cd backend-server
    npm init -y
    ```

2. **Install Required Dependencies**

    Install necessary packages for the backend server:

    ```sh
    npm install express mysql2 sequelize dotenv bcryptjs helmet
    ```

3. **Set Up Project Structure**

    Create necessary directories and files:

    ```sh
    mkdir src
    touch src/server.js src/models.js src/routes.js .env
    ```

4. **Configure Environment Variables**

    Create a `.env` file for your environment variables:

    ```sh
    # backend-server/.env
    PORT=3001
    DB_HOST=mysql
    DB_USER=root
    DB_PASS=password
    DB_NAME=ftp_server
    JWT_SECRET=your_jwt_secret
    ```

5. **Create Database Models**

    Define your MySQL models using Sequelize in `models.js`:

    ```js
    // backend-server/src/models.js
    const { Sequelize, DataTypes } = require('sequelize');
    const sequelize = new Sequelize(process.env.DB_NAME, process.env.DB_USER, process.env.DB_PASS, {
        host: process.env.DB_HOST,
        dialect: 'mysql'
    });

    const User = sequelize.define('User', {
        email: {
            type: DataTypes.STRING,
            allowNull: false,
            unique: true
        },
        password: {
            type: DataTypes.STRING,
            allowNull: false
        }
    });

    const File = sequelize.define('File', {
        name: {
            type: DataTypes.STRING,
            allowNull: false
        },
        path: {
            type: DataTypes.STRING,
            allowNull: false
        }
    });

    User.hasMany(File);
    File.belongsTo(User);

    sequelize.sync();

    module.exports = { User, File, sequelize };
    ```

6. **Set Up the Server**

    Create `server.js` to set up your Express server and define routes:

    ```js
    // backend-server/src/server.js
    const express = require('express');
    const helmet = require('helmet');
    const { User, File } = require('./models');
    const app = express();
    const port = process.env.PORT || 3001;

    app.use(express.json());
    app.use(helmet());

    // Example route to handle user registration
    app.post('/register', async (req, res) => {
        const { email, password } = req.body;
        try {
            const user = await User.create({ email, password });
            res.json(user);
        } catch (error) {
            console.error(error);
            res.status(500).json({ error: 'Failed to register user' });
        }
    });

    // Example route to handle file uploads
    app.post('/upload', async (req, res) => {
        const { userId, fileName } = req.body;
        try {
            const file = await File.create({ name: fileName, path: `/uploads/${fileName}`, UserId: userId });
            res.json(file);
        } catch (error) {
            console.error(error);
            res.status(500).json({ error: 'Failed to upload file' });
        }
    });

    app.listen(port, () => {
        console.log(`Server running on port ${port}`);
    });
    ```

7. **Create Dockerfile for Backend Server**

    Create `Dockerfile` in `backend-server` directory:

    ```Dockerfile
    # backend-server/Dockerfile
    FROM node:14

    # Create app directory
    WORKDIR /usr/src/app

    # Install app dependencies
    COPY package*.json ./
    RUN npm install

    # Bundle app source
    COPY . .

    # Expose the port the app runs on
    EXPOSE 3001

    # Start the application
    CMD ["node", "src/server.js"]
    ```

### Building and Running the Backend Server Container

1. **Build and run the Docker container**:

    Open a terminal in `backend-server` directory and run these commands:

    ```sh
    docker build -t backend-server .
    docker run -p 3001:3001 --env-file .env backend-server
    ```

## Step 2: Set Up the Containerized Web API in Node.js and Express.js

### Initialize the API Project

1. **Create a new directory** for your API and initialize a new Node.js project.

    ```sh
    mkdir api
    cd api
    npm init -y
    ```

2. **Install Required Dependencies**

    Install necessary packages for the API:

    ```sh
    npm install express axios dotenv
    ```

3. **Set Up Project Structure**

    Create necessary directories and files:

    ```sh
    mkdir src
    touch src/server.js .env
    ```

4. **Configure Environment Variables**

    Create a `.env` file for your environment variables:

    ```sh
    # api/.env
    PORT=3002
    BACKEND_SERVER=http://backend-server:3001
    ```

5. **Set Up the API Server**

    Create `server.js` to set up your Express API server:

    ```js
    // api/src/server.js
    const express = require('express');
    const axios = require('axios');
    const app = express();
    const port = process.env.PORT || 3002;

    app.use(express.json());

    // Example route to handle user registration via backend-server
    app.post('/register', async (req, res) => {
        try {
            const response = await axios.post(`${process.env.BACKEND_SERVER}/register`, req.body);
            res.json(response.data);
        } catch (error) {
            console.error(error);
            res.status(500).json({ error: 'Registration failed' });
        }
    });

    // Example route to handle file upload via backend-server
    app.post('/upload', async (req, res) => {
        try {
            const response = await axios.post(`${process.env.BACKEND_SERVER}/upload`, req.body);
            res.json(response.data);
        } catch (error) {
            console.error(error);
            res.status(500).json({ error: 'Upload failed' });
        }
    });

    app.listen(port, () => {
        console.log(`API server running on port ${port}`);
    });
    ```

6. **Create Dockerfile for API**

    Create `Dockerfile` in `api` directory:

    ```Dockerfile
    # api/Dockerfile
    FROM node:14

    # Create app directory
    WORKDIR /usr/src/app

    # Install app dependencies
    COPY package*.json ./
    RUN npm install

    # Bundle app source
    COPY . .

    # Expose the port the app runs on
    EXPOSE 3002

    # Start the application
    CMD ["node", "src/server.js"]
    ```

### Building and Running the API Server Container

1. **Build and run the Docker container**:

    Open a terminal in `api` directory and run these commands:

    ```sh
    docker build -t api .
    docker run -p 3002:3002 --env-file .env api
    ```

## Step 3: Set Up the Containerized React.js Web Application

### Initialize the React.js Project

1. **Create a new directory** for your React.js web application.

    ```sh
    npx create-react-app react-app
    cd react-app
    ```

2. **Update `src/App.js`** to communicate with your API:

    ```jsx
    // react-app/src/App.js
    import React, { useState } from 'react';
    import axios from 'axios';

    function App() {
        const [email, setEmail] = useState('');
        const [password, setPassword] = useState('');

        const handleRegister = async () => {
            try {
                const response = await axios.post('http://localhost:3002/register', { email, password });
                console.log(response.data);
            } catch (error) {
                console.error(error);
            }
        };

        return (
            <div className="App">
                <h1 className="text-2xl font-bold mb-4">Register</h1>
                <input type="text" placeholder="Email" value={email} onChange={(e) => setEmail(e.target.value)} className="border p-2 mb-2" />
                <input type="password" placeholder="Password" value={password} onChange={(e) => setPassword(e.target.value)} className="border p-2 mb-2" />
                <button onClick={handleRegister} className="bg-blue-500 text-white px-4 py-2 rounded">Register</button>
            </div>
        );
    }

    export default App;
    ```

3. **Create Dockerfile for React.js Application**

    Create `Dockerfile` in `react-app` directory:

    ```Dockerfile
    # react-app/Dockerfile
    FROM node:14 as build

    # Set working directory
    WORKDIR /app

    # Copy package.json and package-lock.json
    COPY package*.json ./

    # Install dependencies
    RUN npm install

    # Copy rest of the application
    COPY . .

    # Build React app
    RUN npm run build

    # Production environment
    FROM nginx:alpine

    # Copy build from previous stage
    COPY --from=build /app/build /usr/share/nginx/html

    # Expose port
    EXPOSE 80

    # Default command to start nginx
    CMD ["nginx", "-g", "daemon off;"]
    ```

### Building and Running the React.js Application Container

1. **Build and run the Docker container**:

    Open a terminal in `react-app` directory and run these commands:

    ```sh
    docker build -t react-app .
    docker run -p 3000:80 react-app
    ```

## Step 4: Set Up the Containerized Electron App

### Initialize the Electron Project

1. **Create a new directory** for your Electron application.

    ```sh
    mkdir electron-app
    cd electron-app
    ```

2. **Initialize a new Electron project** using `electron-forge`:

    ```sh
    npx create-electron-app .
    ```

3. **Install Required Dependencies**

    Install necessary packages for Electron app:

    ```sh
    npm install axios
    ```

4. **Update `src/App.js`** to communicate with your API:

    ```jsx
    // electron-app/src/App.js
    const axios = require('axios');

    async function fetchData() {
        try {
            const response = await axios.post('http://localhost:3002/register', { email: 'example@example.com', password: 'password123' });
            console.log(response.data);
        } catch (error) {
            console.error(error);
        }
    }

    fetchData();
    ```

5. **Create Dockerfile for Electron Application**

    Create `Dockerfile` in `electron-app` directory:

    ```Dockerfile
    # electron-app/Dockerfile
    FROM node:14

    # Set working directory
    WORKDIR /usr/src/app

    # Install app dependencies
    COPY package*.json ./
    RUN npm install

    # Bundle app source
    COPY . .

    # Expose necessary ports for Electron
    EXPOSE 3000
    EXPOSE 9222

    # Start the Electron application
    CMD ["npm", "start"]
    ```

### Building and Running the Electron App Container

1. **Build and run the Docker container**:

    Open a terminal in `electron-app` directory and run these commands:

    ```sh
    docker build -t electron-app .
    docker run -p 3000:3000 electron-app
    ```

## Conclusion

You've now set up a complete development environment for containerized applications on Windows 11 using Docker and Visual Studio Code. Each component, including the Node.js web server with MySQL, Node.js Express API, React.js web application with Tailwind CSS, and Electron app, is isolated in its own Docker container. This approach ensures portability and consistency across different environments.

Adjust paths, configurations, and dependencies as per your project requirements. This tutorial assumes basic familiarity with JavaScript and Node.js development. Happy coding!
