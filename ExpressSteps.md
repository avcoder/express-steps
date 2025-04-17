# Express Instructions

1. Create a new folder and go into it via `cd folder-name`
1. `npm init -y`
1. `npm i express`
1. `npm i nodemon`
1. create index.js file with the following code:
    ```js
    const express = require('express');
    const app = express();

    app.get('/', (req, res) => {
      res.send('🚚 Welcome to the Food Truck!');
    });

    app.listen(3000, () => {
      console.log('Server running on http://localhost:3000');
    });
    ```
1. Edit package.json so you can use nodemon.  Inside "scripts" block enter new line:
    ```js
    "start": "nodemon index.js"
    ```
1. In terminal: `npm run start`
1. Move hard coded values to `.env` file (ex: PORT=3000)
   - Remember to `npm i dotenv`
   - then import it via: `require("dotenv").config()`
   - which allows you to use `process.env` now via:  `const PORT = process.env.PORT`
1. If you want to access data sent via POST then you'll have to `npm i body-parser`
   - which will allow you to then say `const bodyParser = require("body-parser")`
   - which you can then use as middleware
    ```js
     app.use(bodyParser.json()); // Middleware to parse JSON data
     app.use(bodyParser.urlencoded({ extended: true }));
    ```
1. Define routes like:
   ```js
    app.get("/orders/:id", (req, res) => {
      console.log("req.params: ", req.params);
   ```
1. or if you wish to get query params such as `/orders/search?customer=alice`
   ```js
    app.get("/orders/search", (req, res) => {
      const { customer, item } = req.query;
   ```
1. or if you wish to get POST data:
   ```js
    app.post("/orders", (req, res) => {
      const { item, quantity, customer } = req.body;
   ```
1. If you wish to send json so that the broser can fetch it, `npm i cors`
   - `const cors = require("cors")`
   - `app.use(cors())`
1. If you wish to send a 404 page for unknown routes
   ```js
      app.use((req, res) => {
      res.status(404).send("404 Not Found");
      });
   ```
1. If you wish to have terminal logs, `npm i morgan`
   ```js
      const morgan = require('morgan');
      const app = express();
      app.use(morgan('dev'));  
   ``` 