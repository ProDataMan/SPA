# SPA
SPA and Micro Front-End Demos

Single Page Application (SPA) demo, focusing on setting up a basic SPA with micro front-ends using Webpack 5. This demo will cover the key concepts of Single-SPA, Micro Front-Ends, and the structure of a modern web application.

### **SPA Demo Setup**

#### **Objective:**
The goal of this demo is to create a simple Single Page Application (SPA) using Micro Front-Ends architecture. We'll use Single-SPA to manage multiple front-end microservices, enabling independent development, deployment, and scaling of individual components.

##### **Step 1: Set Up Your Project Directory**

1. **Create a New Project Directory:**
   - Open your terminal and navigate to the location where you want to create your project. For example, in `C:\Projects`:
   ```bash
   cd C:\Projects
   ```
   - Create a new directory for your SPA demo project and navigate into it:
   ```bash
   mkdir spa-demo
   cd spa-demo
   ```
   
   > **You should now be in the `spa-demo` directory** (`C:\Projects\spa-demo` on Windows).

2. **Initialize a New Node.js Project:**
   - Run the following command to initialize a new Node.js project. This will create a `package.json` file:
   ```bash
   npm init -y
   ```

##### **Step 2: Install Required Dependencies**

1. **Install Single-SPA, Webpack, and Webpack CLI:**
   - Run the following command to install Single-SPA, Webpack, and Webpack CLI as development dependencies:
   ```bash
   npm install single-spa webpack webpack-cli --save-dev
   ```

2. **Install Webpack Dev Server:**
   - Install `webpack-dev-server` to serve your application during development:
   ```bash
   npm install webpack-dev-server --save-dev
   ```

3. **Install React and React-DOM (for Micro Front-Ends):**
   - We will use React to create our Micro Front-End components. Install React and React-DOM:
   ```bash
   npm install react react-dom
   ```

##### **Step 3: Set Up Webpack Configuration**

1. **Create a Webpack Configuration File:**
   - In your `spa-demo` directory, create a file named `webpack.config.js`:
   ```bash
   touch webpack.config.js
   ```

2. **Edit `webpack.config.js` to Configure Webpack and Dev Server:**
   - Open `webpack.config.js` in your text editor and add the following configuration:
   ```javascript
   const path = require('path');
   const HtmlWebpackPlugin = require('html-webpack-plugin');

   module.exports = {
     entry: './src/root-config.js', // Entry point for Single-SPA root configuration
     output: {
       filename: 'bundle.js',
       path: path.resolve(__dirname, 'dist'),
       publicPath: '/',
     },
     mode: 'development',
     devServer: {
       static: {
         directory: path.resolve(__dirname, 'dist'),
       },
       compress: true,
       port: 9000,
       historyApiFallback: true, // SPA routing
     },
     plugins: [
       new HtmlWebpackPlugin({
         template: 'src/index.html',
       }),
     ],
     module: {
       rules: [
         {
           test: /\.js$/,
           exclude: /node_modules/,
           use: {
             loader: 'babel-loader',
             options: {
               presets: ['@babel/preset-react'],
             },
           },
         },
       ],
     },
   };
   ```

3. **Save Your Changes:**
   - Save the changes to `webpack.config.js`.

##### **Step 4: Set Up the Root Configuration for Single-SPA**

1. **Create the Required Directories and Files:**
   - Create the `src` directory:
   ```bash
   mkdir src
   cd src
   ```
   - Inside `src`, create the following files:
   ```bash
   touch root-config.js
   touch index.html
   ```

2. **Edit `index.html` to Add the HTML Structure:**
   - Open `src/index.html` in your text editor and add the following content:
   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <title>SPA Demo</title>
   </head>
   <body>
     <div id="root"></div>
     <script src="bundle.js"></script>
   </body>
   </html>
   ```

3. **Edit `root-config.js` to Register Applications with Single-SPA:**
   - Open `src/root-config.js` and add the following content to register your applications:
   ```javascript
   import { registerApplication, start } from 'single-spa';

   // Register Micro Front-End Applications
   registerApplication({
     name: '@spa/header', // Application name
     app: () => System.import('@spa/header'), // Dynamic import of the application
     activeWhen: ['/'], // When this app should be active
   });

   registerApplication({
     name: '@spa/main', // Application name
     app: () => System.import('@spa/main'), // Dynamic import of the application
     activeWhen: ['/'], // When this app should be active
   });

   // Start the Single-SPA application orchestrator
   start();
   ```

4. **Save Your Changes:**
   - Save the changes to `index.html` and `root-config.js`.

##### **Step 5: Set Up Micro Front-End Applications**

1. **Create Micro Front-End Directories:**
   - In the `src` directory, create separate folders for each micro front-end (e.g., `header` and `main`):
   ```bash
   mkdir header main
   ```

2. **Create `index.js` for Each Micro Front-End:**
   - Inside each micro front-end directory (`header` and `main`), create an `index.js` file:
   ```bash
   cd header
   touch index.js
   cd ../main
   touch index.js
   ```

3. **Add Basic React Components to Each Micro Front-End:**

   - **For `header/index.js`:**
   ```javascript
   import React from 'react';
   import ReactDOM from 'react-dom';

   function Header() {
     return <header><h1>Welcome to the SPA Header!</h1></header>;
   }

   ReactDOM.render(<Header />, document.getElementById('header'));
   ```

   - **For `main/index.js`:**
   ```javascript
   import React from 'react';
   import ReactDOM from 'react-dom';

   function Main() {
     return <main><p>This is the main content area of the SPA.</p></main>;
   }

   ReactDOM.render(<Main />, document.getElementById('main'));
   ```

4. **Update `root-config.js` to Load Components into Specific DOM Elements:**
   - Modify `root-config.js` to include div containers for the micro front-end apps:
   ```javascript
   document.getElementById('root').innerHTML = `
     <div id="header"></div>
     <div id="main"></div>
   `;
   ```

5. **Save Your Changes:**
   - Save all the changes to your micro front-end files and `root-config.js`.

##### **Step 6: Run the Application**

1. **Add Build and Start Scripts to `package.json`:**

   - Open `package.json` and ensure the `"scripts"` section looks like this:
   ```json
   "scripts": {
     "build": "webpack",
     "start": "webpack serve"
   }
   ```

2. **Start the Development Server:**
   - Make sure you are in the root of the `spa-demo` directory, then run:
   ```bash
   npm start
   ```

3. **Open the Application in a Browser:**
   - Open your browser and navigate to `http://localhost:9000`.

#### **What You Should See in the Browser**

- You should see two sections rendered:
  - A header saying **"Welcome to the SPA Header!"**.
  - A main content area with the text **"This is the main content area of the SPA."**.

- If you open the browser console, you should not see any errors related to the application setup or configuration.

### **Conclusion**

By following these steps, you've set up a basic Single Page Application (SPA) with Micro Front-Ends using Single-SPA, React, and Webpack 5. This structure allows each micro front-end to be independently developed and deployed.
