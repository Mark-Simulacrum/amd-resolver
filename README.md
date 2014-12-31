amd-resolver
===========

Resolve AMD module names to a module meta with a File object.

### API

#### Resolver(options : object) : constructor
provides a way to process a configuration such as one from requirejs to convert module names/ids to module meta objects. Module meta objects contain information such as the url for the module, which can be used for retrieving the corresponding file from a remote sever.

@param {object} options - is a configuration object for properly creating module meta objects.  It is compatible with requirejs settings for `paths`, `packages`, `baseUrl`, `shim`, and `urlArgs`.

- @property {string} baseUrl - path every URL that is created is relative to.

- @property {object} paths - is an object with key value pairs to define alias names to files.  These alias are used by the resolver to create a proper url from the name of the module.

  For example, if you wanted to have a module called `md5` and you wanted to map that to the location of the actual file, you can define the following:

  ``` javascript
  {
    "paths": {
      "md5": "path/to/file/module"
    }
  }
  ```

  That will tell the resolver the location for `md5` to create a proper url.

- @property {array} packages - is an array for defining directory aliases to module files. Think npm packages that have an `index.js` or a `main.js`.

  A package can just be a string, in which case if we gave resolver the name of the package, it will generate urls in the form of `packagename/main.js`. That is to say that if you have a package called `machines`, then resolving that package will generate a url to `machinge/main.js`. Alternatively, a package can be an object to provide more granular control over thr url that is generated.

  In order to configure the defaults, you can define a package as an object with the following properties:

  - @property {string} location - which is the location on disk.
  - @property {string} main - file name. Define if `main.js` is not what needs to be loaded.
  - @property {string} name - package name.


- @property {object} shim - maps code in the global object to modules. This is primarily used when code is loaded does that not support an AMD system so the code is in the global space.  An example of this is Backbone.  So, in order to consume Backbone as a module dependency, we need to know how to find it in the global object and possibly dependencies.

  Shims provides two options
  - @property {string} exports | name - The name of the code in the global object.
  - @property {array} deps - List of dependencies.  This is important when the shim needs certain code to be loaded before the shim itself.


##### example

``` javascript
var resolver = new Resolver({
  "urlArgs": 'bust=' + (new Date()).getTime(),
  "baseUrl": "../",
  "paths": {
    "mocha": "../node_modules/mocha/mocha",
    "chai": "../node_modules/chai/chai"
  },
  "shim": {
    "mocha": {
      "exports": "mocha"
    }
  },
  "packages": [
    "pacakge1", {
      "name": "package2"
    }, {
      "location": "good/tests",
      "main": "index",
      "name": "js"
    }, {
      "location": "good/tests",
      "name": "lib"
    }
  ]
});
```

#### resolve(name : string)

Creates a module meta object.

@param {string} name - Name of the module to create a module meta object.

@returns {object} module meta

  - @property {string} `name` - is the name of the module being resolved.
  - @property {File} `file` - is the object that can generate a URL to request the module code from a remote server.
  - @property {string} `urlArgs` - cgi parameters to be used when requesting the module code from a remote server.
  - @property {object} `shim` - which is the an object containing information about the module as it exists in the global object. `shim` can specify a couple of things.
    - @property {string} `exports | name` - which is the name the shim has in the global space.
    - @property {array} `deps` - which is an array of string of dependencies that need to be loaded before the shim.

##### example:

``` javascript
var mochaModuleMeta    = resolver.resolve("mocha"),
    package1ModuleMeta = resolver.resolve("package1");
```

