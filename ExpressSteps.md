# Express Setup Instructions

1. Create a new folder and go into it via `cd folder-name`
1. `npm init -y`
1. `npm i express mongoose morgan dotenv cors`
1. `npm i --save-dev nodemon`
1. create src/server.js file with the following code:
   ```js
   import express from "express";
   import cors from "cors";
   import morgan from "morgan";

   export const app = express();

   app.use(cors());
   app.use(morgan("dev"));
   app.use(express.json()); // no longer need body-parser; not needed after Express v4.16
   app.use(express.urlencoded({ extended: true }));

   app.get("/", (req, res) => {
      res.send("🚚 Welcome to the Food Truck!");
   });

   app.use((req, res) => {
      res.status(404).send("404 Not Found");
   });
   ```
1. Move hard coded values to `.env` file (ex: PORT=3000)
1. Create src/index.js:
   ```js
   import dotenv from "dotenv";
   dotenv.config();
   import { connect } from "./connect.js";

   app.listen(process.env.PORT, () => {
      console.log(`Server running on http://localhost:${process.env.PORT}`);
   });
   ```
1. Since we are using the `import` way (aka ES Module way), we have to edit package.json to have `"type": "module",`
1. Edit package.json so you can use nodemon.  Inside "scripts" block enter new line:
    ```js
    "start": "nodemon ./src/index.js"
    ```
1. In terminal: `npm run start`

# Routes
1. Next we want to define and organize our routes.  
1. Let's first create our callback functions beginning with `getOrders`.  
   - Create handlers folder and order.js file (i.e. src/handlers/order.js):
   ```js
   export const getOrders = async (req, res) => { // notice how we export this function
      // TODO: remove this hard coded order and replace with mongoose db result
      res.json({
         name: "Al",
         order: ["1 fries", "2 tacos"],
         isReady: false,
      });
   };
   ```
1. in /src folder, create router.js :
   ```js
   import { Router } from "express";
   import { getOrders } from "./handlers/order.js";

   export const router = Router(); 

   // Order
   router.get("/order", getOrders);
   ```
   - in server.js, import your above router file `const router = require("./router.js");` 
   - then after your get / route statement, place the following middleware:
   ```js
   app.use('/api', router)
   ```
1. Test: do a GET request to `/api/order` endpoint - do you get JSON back?
1. If above test successful, let's now define rest of our order routes in router.js
   ```js
   // ex: GET /api/orders/2 
   // in router.js
   router.get("/order/:id", getOneOrder);
   // in /handlers/orderj.s
   export const getOneOrder = async (req, res) => {
      const id = req.params.id;
      res.json({ id });
   }

   // ex: POST /api/orders
   // in router.js
   router.post("/order", createOrder);
   // in /handlers/order.js
   export const createOrder = async (req, res) => {
      const order = req.body;
      res.json({ order });
   };

   // ex: DELETE /api/orders/2
   // in router
   router.delete("/order/:id", deleteOrder);
   // in /handlers/orderj.s
   export const deleteOrder = async (req, res) => {
      const deleted = { id: req.params.id };
      res.json({ deleted });
   };

   // ex: PUT /api/orders/
   // in router
   router.put("/order/:id", updateOrder);
   // in /handlers/orderj.s
   export const updateOrder = async (req, res) => {
      // const updated =
      res.json(req.body);
   };

   // ex: GET /search?keyword=bob 
   // in router.js
   router.get("/search", searchOrder);
   // place this function in its own file /src/handlers/search.js 
   export const searchOrder = async (req, res) => {
      // TODO: replace with mongoose db result
      console.log("req.params -- ", req.query);
      res.json({ s: req.query.keyword });
   };
   ```
1. Update `router.js` with new routes.  Then test routes to see if they respond appropriately

# Incorporate Mongoose in Express
1. create a new /src/connect.js file:
   ```js
   const mongoose = require('mongoose');

   export const connect = (url) => mongoose.connect(url);
   ```
1. Copy your mongoDB connection string and put it into .env (ex: DB_CONN=insert-mongodb-connection-string-here)
1. Modify index.js:
   - `import { connect } from "./connect.js";`
   - modify app.listen to be below:
   ```js
   connect(process.env.DB_CONN)
      .then(() => {
         app.listen(process.env.PORT, () => {
            console.log(`Server running on http://localhost:${process.env.PORT}`);
         });
      })
      .catch((e) => console.error(e));
   ```

## Schemas and Models
1. Let's first create a schema for our order.  In /src/models/order.js
   ```js
   import mongoose from "mongoose";

   const orderSchema = mongoose.Schema({
      name: {
         type: String,
         required: [true, "name is required"],
      },
      order: [String],
      isReady: {
         type: Boolean,
         default: false
      }
   });

   export default mongoose.model("order", orderSchema);
   ```
1. With schema in place, Let's now create our order model.  In /src/handlers/order.js, start modifying `getOrders()`
   ```js
   export const getOrders = async (req, res) => {
      const result = await Order.find();
      res.json(result);
   };
   ```
   - test it by using POSTMAN to GET /api/order
1. Repeat with our other custom db functions
   ```js

   ```