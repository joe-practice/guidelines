In order to support [DRYness](http://en.wikipedia.org/wiki/Don't_repeat_yourself) across the increasing number of applications and services using JavaScript in the Wikia ecosystem, all engineers should consider the following guidelines when writing JavaScript modules.

1. [Encapsulation, Explicitness and API](#encapsulation-explicitness-and-api)
2. [Where Should Your Module Live](#where-should-your-module-live)
3. [Packaging and Versioning](#packaging-and-versioning)
4. [Testing](#testing)
5. [Dependencies](#dependencies)

## Encapsulation, Explicitness and API
When developing your JS modules, aim to encapsulate your modules. If your module requires external input, make sure your module exposes a public API for module consumers to pass in that input explicitly. In the past, the organization has been lax on the use of globals in modules. This is an antipattern that breaks encapsulation and makes it so that your module becomes tightly coupled with its application context. *Do not use globals in your modules*.
```javascript
// bad example
function Person () {
	// although this might work in the context you developed your plugin in, it requires
	// other developers to implement this global as well.
	this.name = window.name;
}

var person = new Person();

// good example
function Person (name) {
	// when writing code intended for use in arbitrary environments, prefer the explicit
	// definition and passing of state, rather than binding to state in the global context
	this.name = name;
}

var person = new Person('ken');
```

## Where Should Your Module Live?
Each module should live in its own git repository.
The [jQuery project](https://github.com/jquery/jquery) is a legendary example of a package designed for universal distribution. At the minimum, your project should specify the following:
* `package.json` to manage your projects dependencies
* `tests/` folder containing all the tests for your module
* `dist/` containing the compiled versions of application (these are out of the box drop-in versions)
* `src/` folder is where the development version of your source code will live
* make, jake, rake, gulp, or grunt files: Whatever your build tool's config file is
Example project structure:
```
projectRoot
|- dist/
|- src/
|- tests/
|- package.json
|- gulpfile.js
|- README.md
```
In the dark ages of the monolith™, Wikia developers put all the JavaScript they'd use in the Wikia/app repo. Now that their are multiple JavaScript applications and services living in our ecosystem, we should break out of this dated model and move significantly reusable pieces of code (that have been [properly developed](#encapsulation-explicitness-and-api)) to their own repos. This makes modules easier to [distribute and manage as packages](#packaging-and-versioning).

## Packaging and Versioning 
Developers should use [npm](http://www.npmjs.org) and [bower](http://www.bower.io) (for front-end dependenciess) to manage their JavaScript dependencies. **Use bower to manage packages client-side packages and npm to manage server packages.**
All projects, from full Node.js applications to jQuery plugins should have a valid [package.json](https://www.npmjs.org/doc/files/package.json.html) that uses [semantic versioning](http://semver.org/). This is important so that developers can manage and deploy safe, version-locked modules for their applications. You do not have to have your
module in npm or bower's public repositories to manage your package using npm or bower. Both tools support installing packages from github. For instance:
```bash
// Install package from git with npm install
$ npm install Wikia/foo --save
// with version
$ npm install Wikia/foo#1.4.0 --save

// or in package.json
"dependencies": {
	"foo": "Wikia/foo"
}

// Install bower package from github 
$ bower install https://github.com/user/package.git --save
// with version
$ bower install https://github.com/user/package.git#1.4.0

// or in your bower.json
"dependencies": {
	"foo": "http://bower.io/docs/api/#install"
}
```
For more details on bower's api, [explore this page](ttp://bower.io/docs/api/#install).

## Testing
Your module should be fully unit tested and those tests should live in your modules repo.

## Dependencies
All development dependencies (and for Node.js modules, all dependencies) should be fully managed locally in your module's repo.
