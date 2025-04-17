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
1. 