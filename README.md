# UI_technologies

Package Managers: npm,bower (Download dependecies)
Project Builders(or Task Runners):grunt,gulp (compile css,optimize images,make bundle, minify/transpile)
Project Bundlers: webpack, browserify (bundle different js files into one bundle file)


NPM vs BOWER
------------
NPM and BOWER both are package managers. BOWER is typically used for front-end dependecies. npm is used for server side dependecies.

NPM
---
NPM avoids dependency conflicts with nested dependecies. For ex,
project root
[node_modules] // default directory for dependencies
 -> dependency A
 -> dependency B
    [node_modules]
    -> dependency A

-> dependency C
    [node_modules]
    -> dependency B
      [node_modules]
       -> dependency A 	

BOWER
-----
Bower is optimized for the front-end. Bower uses a flat dependency tree, requiring only one version for each package, reducing page load to a minimum. For ex,
project root
[bower_components] // default directory for dependencies
 -> dependency A
 -> dependency B // needs A
 -> dependency C // needs B and D
 -> dependency D

Note: If two different versions of the same dependecy are required npm will install both, whereas bower will give a conflict. we have to manually pick which version you want. Because saving the resource on a web page is inefficient and costly.

Note:npm requires package.json for defining dependencies, whereas bower requires bower.json.

What is GRUNT?
Ans:
Grunt is a java script task runner. Grunt is configured to project using Gruntfile.js which contains repetitive tasks like minification, compilation, unit testing, linting(code quality) etc.

The Grunt ecosystem is huge. With literally hundreds of plugins to choose from Grunt can be used to automate just about anything with a minimum of effort.

Note: To work with grunt, grunt-cli has to be configured in the system.

New project with Grunt
----------------------
A typical project with grunt contains package.json and Gruntfile.js(or Gruntfile.coffee) at the root directory of the project.

package.json stores metadata for projects published as npm modules. Also, define grunt plugins required for the project in devDependencies section(Ex:npm install grunt-contrib-jshint --save-dev).Here is the sample file.
	{
	  "name": "my-project-name",
	  "version": "0.1.0",
	  "devDependencies": {
		"grunt": "~0.4.5",
		"grunt-contrib-jshint": "~0.10.0",
		"grunt-contrib-nodeunit": "~0.4.1",
		"grunt-contrib-uglify": "~0.5.0"
	  }
	}

Sample Gruntfile
----------------
Every typical javascript library has src(or app),dist(or build) and test folders.
src --> contains source code library.
dist --> contains distribution or minified version of the source code.
test --> contains code to test the project.

During development and releasing new versions there are few tasks that needs to be performed on regular basis. Here are few examples.

code quality --> To ensure that code adheres to best practices and to avoid unexpected behaviours employ a tool like jshint. grunt has an official plugin for jshint named 'grunt-contrib-jshint'.

watch for changes -->  whenever code is modified to make sure to not break any rules and best practices, good strategy is to check the code at every change. To do this, grunt has a plugin named 'grunt-contrib-watch'. This can run the predefined task 'grunt-contrib-jshint' if configured.

testing --> checking the source code for stability and eliminating bugs needs testing. There are several libraries like Qunit and Jasmine. Grunt has plugin named 'grunt-contrib-qunit'.

minifying --> To distribute the project which reduces amount of data to be transferred, minification is required. Grunt has a plugin named 'grunt-contrib-uglify'.

Setting up Gruntfile
--------------------
Gruntfile contains a function which encapsulates configuration. Here is the syntax:
	module.exports = function(grunt) {};

Intialization
-------------
Initial configuration to load package.json file into some property, so that it can be referred in other tasks.
	module.exports = function(grunt) {
	  grunt.initConfig({
		pkg: grunt.file.readJSON('package.json')
	  });
	};

Tasks
-----
Concatenation Task
------------------
For concatenating different javascript files into one file, grunt has a plugin named 'grunt-contrib-concat'. Here is the configuration:
	concat: {
	  options: {
		// define a string to put between each file in the concatenated output
		separator: ';'
	  },
	  dist: {
		// the files to concatenate
		src: ['src/**/*.js'],
		// the location of the resulting JS file
		dest: 'dist/<%= pkg.name %>.js'
	  }
	}
	
Note: The configuration code for a plugin typically shares the same name as that of plugin. i.e. 'grunt-contrib-concat' has a property named 'concat' in Gruntfile.

Minifying Task
--------------
Grunt plugin name is 'grunt-contrib-uglify'. Here is the configuration.

	uglify: {
	  options: {
		// the banner is inserted at the top of the output
		banner: '/*! <%= pkg.name %> <%= grunt.template.today("dd-mm-yyyy") %> */\n'
	  },
	  dist: {
		files: {
		  'dist/<%= pkg.name %>.min.js': ['<%= concat.dist.dest %>']
		}
	  }
	}

Testing Task
------------
Grunt plugin name for testing using qunit is 'grunt-contrib-qunit'. Here is the configuration.
	qunit: {
		files: ['test/**/*.html']
	}

Code verification Task
----------------------
Grunt plugin name for code quality using jshint is 'grunt-contrib-jshint'.  JSHint is a tool that can detect issues or potential issues like a high cyclomatic complexity, the use of the equality operator instead of the strict equality operator, and the definition of unused variables and functions. 

It is advisable to analyze all javascript files including Gruntfile and test files. Here is the configuration.
	jshint: {
	  // define the files to lint
	  files: ['Gruntfile.js', 'src/**/*.js', 'test/**/*.js'],
	  // configure JSHint (documented at http://www.jshint.com/docs/)
	  options: {
		// more options here if you want to override JSHint defaults
		globals: {
		  jQuery: true,
		  console: true,
		  module: true
		}
	  }
	}	

Watch Task
----------
Grunt plugin name for watch task is 'grunt-contrib-watch'. This task is configured to run other tasks like jshint and qunit whenever a javascript file is added,modified or deleted. Here is the configuration:
	watch: {
	  files: ['<%= jshint.files %>'],
	  tasks: ['jshint', 'qunit']
	}

Note: To run the watch task command is 'grunt watch'.
Load the plugins
----------------
To load the grunt plugins we have configured above, it has be to done through loadNPMTasks method like this.
	grunt.loadNpmTasks('grunt-contrib-uglify');
	grunt.loadNpmTasks('grunt-contrib-jshint');
	grunt.loadNpmTasks('grunt-contrib-qunit');
	grunt.loadNpmTasks('grunt-contrib-watch');
	grunt.loadNpmTasks('grunt-contrib-concat');

Creating Tasks
--------------
To setup default task and test task, here is the code:
	// this would be run by typing "grunt test" on the command line
	grunt.registerTask('test', ['jshint', 'qunit']);

	// the default task can be run just by typing "grunt" on the command line
	grunt.registerTask('default', ['jshint', 'qunit', 'concat', 'uglify']);

Note: For more info refer to https://gruntjs.com/sample-gruntfile

what is gulp?
Ans: Gulp is also a task runner like grunt. Unlike Grunt, gulp uses javascript for configuring tasks instead of json syntax. Here are the basic repetitive tasks required during most of the development.
1)generating HTML from templates and content files
2)compressing new and modified images
3)compiling Sass to CSS code
4)removing console and debugger statements from scripts
5)transpiling ES6 to cross-browser-compatible ES5 code
6)code linting and validation
7)concatenating and minifying CSS and JavaScript files
8)deploying files to development, staging and production servers

Install Gulp command-line interface globally so the gulp command can be run from any project folder using 'npm install gulp-cli -g'.

Configuring a project
---------------------
1)cd project_dir
2)npm init --> package.json file will be created after this which stores all npm config settings.
3)create src(or app),build(or dest),test directories
4)create html,images,js,scss directories in src directory.
5)Install gulp locally using the command 'npm install gulp --save-dev'. This installs gulp as a dev dependency and the 'devDependencies' section of package.json is updated.

Note: Dev dependecies are not installed when NODE_ENV env variable set as production. i.e. In Unix, export NODE_ENV=production. For windows, set NODE_ENV=production.

Note:some tasks like minification are run on production. In that case, use 'npm install gulp --save'.

Configuring gulp
----------------
Create a new file Gulpfile.js in the project root directory. Here is the basic code.
	// Gulp.js configuration
	var gulp = require('gulp');
	// development mode?
	var devBuild = (process.env.NODE_ENV !== 'production');
    // folders
	var folder = {src: 'src/', build: 'build/'};

Creating gulp tasks
-------------------
Gulp does nothing on its own. We must
1)Install gulp plugins 
2)write tasks which utilize those plugins to do something useful.

Note:It is possible to write own plugins, But there are more than 3 thousand plugins already available. It's mostly unlikely that we ever need one. To search for existing plugins 
'http://gulpjs.com/plugins/'.

Gulp contains three primary functions.
gulp.task – defines a new task with a name, optional array of dependencies and a function.
gulp.src – sets the folder where source files are located.
gulp.dest – sets the destination folder where build files will be placed.

Image compression task
----------------------
This is best demonstrated with an example so let’s create a basic task which compresses images and copies them to the appropriate build folder. Since this process could take time, we’ll only compress new and modified files. 

Two plug-ins can help us: gulp-newer and gulp-imagemin. Install them from the command-line:
"npm install gulp-newer gulp-imagemin --save-dev".

Task configuration
------------------
	var gulp = require('gulp');
	var folder = {src: 'src/', build: 'build/'};
	var newer = require('gulp-newer');
	var imagemin = require('gulp-imagemin');
	// image processing
	gulp.task('images', function() {
	  var out = folder.build + 'images/';
	  return gulp.src(folder.src + 'images/**/*')
		.pipe(newer(out))
		.pipe(imagemin({ optimizationLevel: 5 }))
		.pipe(gulp.dest(out));
	});

Note:The beauty of this is in the .pipe() chaining. You take a set of input files, pipe them through a series of transformations, then return the output files. 

To run the task from command line 'gulp images'.

HTML task
---------
Like Image compression, html clean task minifies html(removes unnecessary white spaces) and attributes using gulp-htmlclean plugin. command to install htmlclean 'npm install gulp-htmlclean --save-dev'.

Task configuration
------------------
	var htmlclean = require('gulp-htmlclean');
	// HTML processing
	gulp.task('html', ['images'], function() {
	  var
		out = folder.build + 'html/',
		page = gulp.src(folder.src + 'html/**/*')
		  .pipe(newer(out));

	  // minify production code
	  if (!devBuild) {
		page = page.pipe(htmlclean());
	  }

	  return page.pipe(gulp.dest(out));
	});	

Note: glup-newer is reused. The [images] argument means that images task must be run before processing HTML.Any number of dependent tasks can be listed in this array and all will complete before the task function runs.

Note: Run 'gulp html' from the command line. Both html and images task will run.

JavaScript Task
---------------
Lets build a module bundler task to do the following things.
1)Ensure all dependencies are loaded using gulp-deporder plugin. (gulp-deporder analyses comments on top of each javascript file to ensure correct ordering).
2)concatenate all javascript files into a single main.js using gulp-concat.
3)remove all console and debugging statements with gulp-strip-debug and minimize code with gulp-uglify. This step is run typically in production.

Install plugins using 'npm install gulp-deporder gulp-concat gulp-strip-debug gulp-uglify --save-dev'.

Task configuration
------------------
	var concat = require('gulp-concat');
    var deporder = require('gulp-deporder');
	var stripdebug = require('gulp-strip-debug');
	var uglify = require('gulp-uglify');
	// JavaScript processing
	gulp.task('bundle', function() {

	  var jsbuild = gulp.src(folder.src + 'js/**/*')
		.pipe(deporder())
		.pipe(concat('main.js'));

	  if (!devBuild) {
		jsbuild = jsbuild
		  .pipe(stripdebug())
		  .pipe(uglify());
	  }

	  return jsbuild.pipe(gulp.dest(folder.build + 'js/'));
	});

Note: Run the task with the command 'gulp bundle'.

Task automation
---------------
Instead of running one command at a time, we can run all with one command by adding a new run command like this.
	gulp.task('run', ['html', 'css', 'js']);

Note: images is already part of html. No need to mention it seperately.

Gulp watch task
---------------
Gulp watch can monitor source files and run an appropriate task whenever a file is changed. Here is the configuration.
	// watch for changes
	gulp.task('watch', function() {

	  // image changes
	  gulp.watch(folder.src + 'images/**/*', ['images']);

	  // html changes
	  gulp.watch(folder.src + 'html/**/*', ['html']);

	  // javascript changes
	  gulp.watch(folder.src + 'js/**/*', ['js']);

	  // css changes
	  gulp.watch(folder.src + 'scss/**/*', ['css']);

	});

Default task
------------
Rather than running gulp watch, let's add a default like this:
	gulp.task('default', ['watch']);
	
Note: Run 'gulp' from command line without any arguments. This will run the default task.

Note:Gulp can also call other Node.js modules – they don’t necessarily need to be plug-ins. Ex:
1)browser-sync – automatically reload assets or refresh your browser when changes occur
2)del – delete files and folders (perhaps clean your build folder at the start of every run).

For more reference on gulp : https://www.sitepoint.com/introduction-gulp-js/

What is the difference between grunt vs gulp?
Ans:
The first to achieve critical mass was Grunt – a Node.js task runner which used plug-ins controlled (originally) by a JSON configuration file. Grunt was hugely successful but there were a number of issues:

Grunt required plug-ins for basic functionality such as file watching.
Grunt plug-ins often performed multiple tasks which made customisation more awkward.
JSON configuration could become unwieldy for all but the most basic tasks.
Tasks could run slowly because Grunt saved files between every processing step.
Many issues were addressed in later editions but Gulp had already arrived and offered a number of improvements:

Features such as file watching were built-in.
Gulp plug-ins were (mostly) designed to do a single job.
Gulp used JavaScript configuration code which was less verbose, easier to read, simpler to modify, and provided better flexibility.
Gulp was faster because it uses Node.js streams to pass data through a series of piped plug-ins. Files were only written at the end of the task.

Conclusion:
-----------
Grunt vs gulp (is like maven vs. gradle or configuration vs. code). Grunt is based on configuring separate independent tasks, each task opens/handles/closes file. Gulp requires less amount of code and is based on Node streams, which allows it to build pipe chains (w/o reopening the same file) and makes it faster.

what is webpack?
Ans:
Webpack is a module bundler.It has nothing to do with a tasks runner, although in many cases can substitute the need of gulp or grunt with loaders. Webpack understands about modules and its dependencies (among JavaScript files, CSS or whatever) and generates assets.

Webpack is such a powerful tool that it can already perform the vast majority of the tasks you’d otherwise do through a task runner. For instance, Webpack already provides options for minification and sourcemaps for your bundle. In addition, Webpack can be run as middleware through a custom server called webpack-dev-server, which supports both live reloading and hot reloading. By using loaders, you can also add ES6 to ES5 transpilation, and CSS pre- and post-processors. That really just leaves unit tests and linting as major tasks that Webpack can’t handle independently. 

Note: With Webpack half a dozen potential gulp tasks down to two, many devs opt to use NPM scripts with webpack, as this avoids the overhead of adding Gulp to the project.

Installing webpack
------------------
Installing webpack using npm command line: 'npm install webpack -g'.

Create a sample project
-----------------------
1)create file entry.js with below content in root directory of the project`.
	document.write("It works.");
2)create a file index.html in the root directory with below content.
	<html>
		<head>
			<meta charset="utf-8">
		</head>
		<body>
			<script type="text/javascript" src="bundle.js" charset="utf-8"></script>
		</body>
	</html>
3)Run the command 'webpack ./entry.js bundle.js'. 

Note: Step 3 will compile your file and create a bundle file. Open index.html in browser. It should display 'It works'.

Add more content to Project
---------------------------
1)Create a file named content.js with following code:
	module.exports = "It works from content.js";
2)modify entry.js with below data. remove existing 
	document.write(require("./content.js"));
3)Run the command 'webpack ./entry.js bundle.js'. 

Open index.html in browser to see the change in display.

*****
Note:webpack will analyze your entry file for dependencies to other files. These files (called modules) are included in your bundle.js too. webpack will give each module a unique id and save all modules accessible by id in the bundle.js file. Only the entry module is executed on startup.

Loaders
-------
Loaders are kind of like “tasks” in other build tools, and provide a powerful way to handle frontend build steps. Loaders can transform files from a different language like, CoffeeScript to JavaScript, or inline images as data URLs. 

Note: webpack can only handle JavaScript natively. Loaders are typically javascript functions. For ex, to process css files we need some loader like 'css-loader' which will understand processing of .css files.

Let's add some css to our above project.
1)Install loaders with npm command line.
	npm install css-loader style-loader
2) create a file style.css with below code:
	body {
		background: yellow;
	}
3) Add below line in entry.js at the top.
	require("!style-loader!css-loader!./style.css");

Recompile and update browser to see style changes.	
*****
Note: Different loaders are seperated in require statement using (!) symbol. Also, these loaders are processed from right to left. i.e. First style.css is loaded, then css-loader is applied on that followed by style-loader.

Note:If you don't want long require methods for loaders, we can run loaders through command line like this.
	webpack ./entry.js bundle.js --module-bind 'css=style-loader!css-loader'

Configuration File
------------------
we can move the configuration to a file. Lets create a file webpack.config.js. Add the following code.
	module.exports = {
		entry: "./entry.js",
		output: {
			path: __dirname,
			filename: "bundle.js"
		},
		module: {
			loaders: [
				{ test: /\.css$/, loader: "style!css" }
			]
		}
	};

Now simply run 'webpack' without any arguments.

Note: If project grows big, then compilation takes lot of time. To display the progress, we can use 'webpack --progress --colors'.

Note: If you don't want to manually recompile after each change, we can use watch like this.
'webpack --progress --colors --watch'.

Configuring a loader
--------------------
Each loader can have these properties.
1)test: A condition that must be met
2)exclude: A condition that must not be met
3)include: An array of paths or files where the imported files will be transformed by the loader
4)loader: A string of “!” separated loaders
5)loaders: An array of loaders as string

Example for jsx transformation to js.
	module: {
	  loaders: [
		{
		  // "test" is commonly used to match the file extension
		  test: /\.jsx$/,

		  // "include" is commonly used to match the directories
		  include: [
			path.resolve(__dirname, "app/src"),
			path.resolve(__dirname, "app/test")
		  ],

		  // "exclude" should be used to exclude exceptions
		  // try to prefer "include" when possible

		  // the "loader"
		  loader: "babel-loader" // or "babel" because webpack adds the '-loader' automatically
		}
	  ]
	}

IMPORTANT: The loaders here are resolved relative to the resource which they are applied to. This means they are not resolved relative to the configuration file. If you have loaders installed from npm and your node_modules folder is not in a parent folder of all source files, webpack cannot find the loader. You need to add the node_modules folder as an absolute path to the resolveLoader.root option. (resolveLoader: { root: path.join(__dirname, "node_modules") })

Note:An array of applied pre and post loaders can be defined with preLoaders and postLoaders.

WRITING A LOADER
----------------
Writing a loader is pretty simple. A loader is just a file that exports a function.


what is Browserify?
Ans: Browserify is a development tool for bundling that allows us to write node.js-style modules that compile for use in the browser. we write our modules in separate files, exporting external methods and properties using the module.exports and exports variables. We can even require other modules using the require function, and if we omit the relative path it’ll resolve to the module in the node_modules directory. Here is an example:

multiply.js
-----------
	module.exports = function (a, b) {
		return a * b;
	};

square.js
---------
	var multiply = require('./multiply');
	module.exports = function (n) {
	  return multiply(n, n);
	};

index.js
--------
	var square = require('./square');
	console.log(square(125));	

We have couple of modules require each other. we can run browserify and generate the file for use in the browser:
	browserify index.js -o bundle.js

we have a bundle.js file that bundled the three modules. we can add a single script tag reference to it into our html page and it’ll execute in the browser automatically resolving require calls.	
	<script src="bundle.js"></script>



1)what are different transpilers?
2)Difference between ES5 and ES6?
3)Difference between CommonJS and AMD?
4)Difference between requirejs and browserify?
