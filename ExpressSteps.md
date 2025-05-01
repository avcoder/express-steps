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
   ```
1. Create `/src/connect.js`
   ```js
   import mongoose from "mongoose";
   
   export const connect = (url) => mongoose.connect(url); // to be imported in index.js
   ```
1. Move any hard coded values to `.env` file (ex: PORT=3000, DB connection string)
1. Create src/index.js:
   ```js
   import dotenv from "dotenv";
   dotenv.config();
   import { connect } from "./connect.js";
   import { app } from "./server.js";
   
   connect(process.env.DB_CONN)
   .then(() => {
      app.listen(process.env.PORT, () => {
         console.log(`Server running on http://localhost:${process.env.PORT}`);
      });
   })
   .catch((e) => console.error(e));
   ```
1. Since we are using the `import` way (aka ES Module way), we have to edit package.json to have `"type": "module",`
1. Edit package.json so you can use nodemon.  Inside "scripts" block enter new line:
    ```js
    "start": "nodemon ./src/index.js"
    ```
1. In terminal: `npm run start`

# Routes
1. in /src folder, create router.js:
   ```js
   import { Router } from "express";
   import orderController from "./controllers/orderController.js"; // will direct traffic
   
   export const router = Router(); 
   
   // Order
   router.get("/order", orderController.getOrders);
   
   // TODO: Fill in rest of CRUD routes
   ```
   - in server.js, import your above router file `import { router } from "./router.js"`
   - then after your `app.get('/') statement, place the following middleware:
   ```js
   app.use('/api', router)
   ```
1. Next let's developer our orderController.getOrders function. Create `/src/controllers/orderController.js`
   ```js
    import orderHandler from "../handlers/order.js";

    const getOrders = async (req, res) => {
      const orders = await orderHandler.getAllOrders();  // will call our models
      res.status(200).json(orders);
    };

    // TODO: implement rest of CRUD controllers

    export default {
      getOrders,
      // TODO: export rest of CRUD controllers
    };
    ```
1. Create `src/handlers/order.js`
   ```js
   import Order from "../models/order.js";  // will handle DB interactions
   
   let receiptNo = 1;
   
   // Get all
   const getAllOrders = async () => {
     return await Order.find().lean();
   };
   
   // TODO: implement rest of CRUD handlers
   
   export default {
     getAllOrders,  // TODO: export rest of CRUD handlers
   };
   ```
1. Create `/src/models/order.js`
   ```js
   import mongoose from "mongoose";

   const orderSchema = mongoose.Schema({
    name: {
      type: String,
      required: [true, "name is required"],
    },
    order: {
      type: [String],
      required: [true, "order is required"],
      default: [],
    },
    isReady: {
      type: Boolean,
      required: true,
      default: false,
    },
    receiptId: String,
   });

  export default mongoose.model("order", orderSchema);
  ```
1. Test our app so far: do a GET request to /api/order endpoint - do you get JSON back?
