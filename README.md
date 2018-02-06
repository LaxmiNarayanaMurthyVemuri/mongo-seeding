![Mongo Seeding](https://raw.githubusercontent.com/pkosiec/mongo-seeding/master/assets/logo.png)

# Mongo Seeding
[![npm version](https://badge.fury.io/js/mongo-seeding.svg)](https://npmjs.org/package/mongo-seeding) [![Build Status](https://travis-ci.org/pkosiec/mongo-seeding.svg?branch=master)](https://travis-ci.org/pkosiec/mongo-seeding) [![David](https://img.shields.io/david/pkosiec/mongo-seeding.svg)]() [![David](https://img.shields.io/david/dev/pkosiec/mongo-seeding.svg)]() [![Codacy Badge](https://api.codacy.com/project/badge/Grade/9960aeeba19d4992b0df8781cd580eec)](https://www.codacy.com/app/pkosiec/mongo-seeding?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=pkosiec/mongo-seeding&amp;utm_campaign=Badge_Grade) 

Fill your MongoDB database with data in easy way. Use JavaScript and JSON files to define the data!

## Looking for different type of database seed solution?
- Docker image (TypeScript support included): [Mongo Seeding Docker Image](https://github.com/pkosiec/mongo-seeding-docker)
- Command line tool: [Mongo Seeding CLI](https://github.com/pkosiec/mongo-seeding-cli)

## Install
```bash
npm install mongo-seeding --save
```

## Usage
1. Import `seedDatabase` function:
    ```javascript
    const { seedDatabase } = require('mongo-seeding');
    ```
1. Define partial configuration object. The object will be merged with default config object (see [Configuration](#configuration) section). Therefore, you can specify only the keys with different values than default ones.

    ```javascript
    const path = require('path');

    const config = {
      database: {
        host: '127.0.0.1',
        port: 27017,
        name: 'mydatabase',
      },
      dataPath: path.resolve(__dirname, '../data'),
      dropDatabase: true,
    };
    ```

1. Seed your database!
  - with `async/await`, i.e.:

    ```javascript
    (async () => {
      try {
        await seedDatabase(config);
      } catch (err) {
        // Handle errors
      }
      // Do whatever you want after successful import
    })()    
    ```

  - with raw promises:
  
    ```javascript
    seedDatabase(config).then(() => {
      // Do whatever you want after successful import
    }).catch(err => {
      // Handle errors
    });
    ```

## Configuration
You can overwrite any configuration property you want by passing partial config object to `seedDatabase` function.

The object passed as a parameter for `seedDatabase` method is merged with default configuration object. You can pass `{}` to use all default settings.

**Default configuration object**:

```javascript
const config = {
  database: {
    protocol: 'mongodb',
    host: '127.0.0.1',
    port: 27017,
    name: 'database',
    username: undefined,
    password: undefined,
  },
  dataPath: path.resolve(__dirname, '../data'),
  dropDatabase: false,
  replaceIdWithUnderscoreId: false,
  supportedExtensions: ['json', 'js'],
  reconnectTimeoutInSeconds: 10,
};
```

## Debug output
To see debug output just set environmental variable `DEBUG` to `mongo-seeding` before starting your Node.js app:

```bash
DEBUG=mongo-seeding node yourapp/index.js
```

or programmatically before requiring `mongo-seeding`:

```javascript
process.env.DEBUG = 'mongo-seeding';
const { seedDatabase } = require('mongo-seeding');
```

## Preparing data to import
1. Create a new base directory. In this example, it'll be named `data`.
1. Define a few collections via creating subdirectories in `data` directory. New collection will be created if it doesn't exist in database.

    **Naming convention**
    - If you don't care about import order - just name directories simply with collection names - i.e. `categories`, `posts`, `comments`, etc.   
    - To keep your own import order, name directories as `$IMPORT_NUMBER-$COLLECTION_NAME` - i.e. `1-categories`, `2-posts`, `3-comments`. Import number has to be separated with collection name via dash (`-`).

1. We have collections - now it's time to define documents. It can be done via creating files in collections directories.

    **A few things to know**: 
    - Collection directory can contain multiple files
    - Every file can contain single objects or array of objects
    - One object represents one MongoDB document
    - Supported extensions: `js`, `json`
    - In `js` files export object or array of objects via `module.exports =   objectOrArray`.

    **Some examples**:

    `object.js` ( will result in creating single MongoDB document):
    ```js
    module.exports = {
      name: "Parrot"
    }
    ```

    `array.js` (it will create 2 documents):
    ```js
    module.exports = [
      {
        name: "Dog"
      },
      {
        name: "Cat"
      }
    ]
    ```

    `object.json` (represent 1 MongoDB document):
    ```json
    {
      "name": "Penguin",
    }
    ```

    `array.json` (creates two different documents):
    ```json
    [
      {
        "name": "Hamster"
      },
      {
        "name": "Crocodile"
      }
    ]
    ```

1. The complete file structure should look like this:

    ```
    data
    +-- 1-categories
    |   +-- cat.js
    |   +-- dogs.js
    |   +-- other-animals.json
    +-- 2-posts
    |   +-- post-about-my-cat.json
    |   +-- dog-posts.js
    |   +-- random-stuff.js
    +-- 3-media
    |   +-- cat-image.js
    |   +-- dog.js
    ```

1. To sum everything up: Subdirectories of base directory represent database collections. Files in collection directories represent documents. Simple as that.

 ## Samples
 Take a look at [samples repository](https://github.com/pkosiec/mongo-seeding-samples) to see more examples how to define data structure properly!
