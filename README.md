### **SPA Demo Setup**

These instructions will guide you through setting up a Single-SPA demo project from scratch, including configuring Webpack, installing necessary dependencies, setting up micro front-ends, and running the application.

---

#### **Step 1: Set Up Your Project Directory**

1. **Create a New Project Directory:**

   Open your terminal and navigate to the location where you want to create your project. For example, in `C:\Projects`:

   ```bash
   cd C:\Projects
   ```

   Create a new directory for your SPA project and navigate into it:

   ```bash
   mkdir spa
   cd spa
   ```

2. **Initialize a New Node.js Project:**

   Run the following command to initialize a new Node.js project. This will create a `package.json` file:

   ```bash
   npm init -y
   ```

---

#### **Step 2: Install Required Dependencies**

1. **Install Single-SPA, Webpack, and Webpack CLI:**

   Run the following command to install Single-SPA, Webpack, and Webpack CLI as dependencies:

   ```bash
   npm install single-spa webpack webpack-cli --save
   ```

2. **Install Webpack Dev Server and HTML Webpack Plugin:**

   Install `webpack-dev-server` and `html-webpack-plugin` to serve your application during development:

   ```bash
   npm install webpack-dev-server html-webpack-plugin --save-dev
   ```

3. **Install Babel Loader and Related Packages:**

   We will use Babel to transpile JavaScript and JSX. Install `babel-loader` and necessary Babel presets:

   ```bash
   npm install babel-loader @babel/core @babel/preset-env @babel/preset-react --save-dev
   ```

4. **Install React and React-DOM (for Micro Front-Ends):**

   If you are using React for your micro front-ends, install React and React-DOM:

   ```bash
   npm install react react-dom --save
   ```

---

#### **Step 3: Set Up Webpack Configuration**

1. **Create a Webpack Configuration File:**

   In your `spa` directory, create a file named `webpack.config.js`:

   ```bash
   touch webpack.config.js
   ```

2. **Edit `webpack.config.js` to Configure Webpack and Dev Server:**

   Open `webpack.config.js` in your text editor and add the following configuration:

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
       static: path.resolve(__dirname, 'dist'),
       compress: true,
       port: 9000,
       historyApiFallback: true, // Ensures all requests go to index.html for SPA routing
     },
     plugins: [
       new HtmlWebpackPlugin({
         template: 'src/index.html', // Reference to your HTML file
       }),
     ],
     module: {
       rules: [
         {
           test: /\.js$/, // Apply this rule to .js files
           exclude: /node_modules/, // Exclude node_modules from transpilation
           use: {
             loader: 'babel-loader', // Use babel-loader for transpiling
             options: {
               presets: ['@babel/preset-env', '@babel/preset-react'], // Babel presets
             },
           },
         },
       ],
     },
   };
   ```

3. **Save Your Changes:**

   Save the changes to `webpack.config.js`.

---

#### **Step 4: Set Up the Root Configuration for Single-SPA**

1. **Create the Required Directories and Files:**

   Create the `src` directory:

   ```bash
   mkdir src
   cd src
   ```

   Inside `src`, create the following files:

   ```bash
   touch root-config.js index.html
   ```

2. **Edit `index.html` to Add the HTML Structure:**

   Open `src/index.html` in your text editor and add the following content:

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
     <!-- Ensure SystemJS is loaded before the bundle -->
     <script src="https://cdn.jsdelivr.net/npm/systemjs@6.10.2/dist/system.min.js"></script>
     <script>
       System.config({
         map: {
           '@spa/header': 'http://localhost:9001/header.js',
           '@spa/main': 'http://localhost:9002/main.js'
         }
       });
     </script>
     <script src="bundle.js"></script> <!-- Ensure this script is correctly linked -->
   </body>
   </html>
   ```

3. **Edit `root-config.js` to Register Applications with Single-SPA:**

   Open `src/root-config.js` and add the following content to register your applications:

   ```javascript
   import { registerApplication, start } from 'single-spa';

   // Register Micro Front-End Applications
   registerApplication({
     name: '@spa/header',
     app: () => window.System.import('@spa/header'),
     activeWhen: ['/'],
   });

   registerApplication({
     name: '@spa/main',
     app: () => window.System.import('@spa/main'),
     activeWhen: ['/'],
   });

   // Initialize the DOM elements where the micro front-ends will be mounted
   document.getElementById('root').innerHTML = `
     <div id="header"></div>
     <div id="main"></div>
   `;

   // Start the Single-SPA application orchestrator
   start();
   ```

4. **Save Your Changes:**

   Save the changes to `index.html` and `root-config.js`.

---

#### **Step 5: Set Up Micro Front-End Applications**

1. **Create Micro Front-End Directories:**

   In the `src` directory, create separate folders for each micro front-end (e.g., `header` and `main`):

   ```bash
   mkdir header main
   ```

2. **Create `index.js` for Each Micro Front-End:**

   Inside each micro front-end directory (`header` and `main`), create an `index.js` file:

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

   // Mount function to start up the micro front-end
   export function mount(props) {
     console.log('Mounting @spa/header');
     ReactDOM.render(<Header />, document.getElementById('header'));
   }

   // Unmount function to clean up the micro front-end
   export function unmount(props) {
     console.log('Unmounting @spa/header');
     ReactDOM.unmountComponentAtNode(document.getElementById('header'));
   }
   ```

   - **For `main/index.js`:**

   ```javascript
   import React from 'react';
   import ReactDOM from 'react-dom';

   function Main() {
     return <main><p>This is the main content area of the SPA.</p></main>;
   }

   // Mount function to start up the micro front-end
   export function mount(props) {
     console.log('Mounting @spa/main');
     ReactDOM.render(<Main />, document.getElementById('main'));
   }

   // Unmount function to clean up the micro front-end
   export function unmount(props) {
     console.log('Unmounting @spa/main');
     ReactDOM.unmountComponentAtNode(document.getElementById('main'));
   }
   ```

4. **Save Your Changes:**

   Save all the changes to your micro front-end files and `root-config.js`.

---

#### **Step 6: Run the Application**

1. **Add Build and Start Scripts to `package.json`:**

   Open `package.json` and ensure the `"scripts"` section looks like this:

   ```json
   "scripts": {
     "build": "webpack",
     "start": "webpack serve"
   }
   ```

2. **Start the Development Server:**

   Make sure you are in the root of the `spa` directory, then run:

   ```bash
   npm start
   ```

3. **Open the Application in a Browser:**

   Open your browser and navigate to `http://localhost:9000`.

---

#### **Step 7: Verify the Application in the Browser**

1. **Check the UI:**

   Ensure that the UI elements from your micro front-end applications (like the header and main content) are visible.

2. **Check the Console and Network Tab:**

   - **Console Tab**: Look for any JavaScript errors or warnings.


   - **Network Tab**: Ensure all JavaScript files (`header.js`, `main.js`, etc.) are loaded without 404 errors.

3. **Inspect HTML Structure:**

   - Open the **Elements** tab in Developer Tools and verify that the `div` elements (`<div id="header"></div>`, `<div id="main"></div>`) exist.

---

#### **Step 8: Build the Application for Production**

1. **Run the Build Command:**

   Run the following command to build the application for production:

   ```bash
   npm run build
   ```

2. **Serve the Built Application:**

   - Serve the contents of the `dist` folder using a static server:
   
   ```bash
   npx serve -s dist
   ```

   - Open your browser and navigate to the served address (usually `http://localhost:5000`).

---

### **Summary**

By following these instructions, you will set up a Single-SPA project with multiple micro front-end applications, configure Webpack and Babel.
