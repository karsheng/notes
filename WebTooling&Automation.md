# Web Tooling & Automation
1. IDE vs. Editor
  1. IDE (Integrated Development Environment) - attempt to do it all: editing, debugging, building, compiling, and optimizing. Successful IDE: iOS and Android.
1. Popular: Sublime Text and Atom.

## Useful Shortcuts
1. command + shift + P - open the command palette - useful for shortcuts and package control (sublime)
1. command + T - Recently opened files
1. command + R - search for symbols: useful for CSS or JavaScript
1. command + alt + G - search for next instance of highlighted text
1. command + D - highlight the next instance of the highlighted word, repeat for the next and the next and the next...
1. command + ctrl + G - selected all instances of highlighted text i.e. global selection
1. use emmet - example
  ```HTML
  <!-- ul#nav>li*4 + tab gives-->
  <ul id="nav">
		<li></li>
		<li></li>
		<li></li>
		<li></li>
	</ul>
  ```

## Build tools
1. The most simple build tools is  just a shell, or bash script.
1. These scripts, with the .sh extension, are just a series of terminal commands or functions and can be executed from the command line.
1. Apache Ant, Maven and gradle are popular build tools in the world of Java.
1. Web development build tools are mostly JavaScript based - node JS based build tools. Grunt and Gulp has become the most popular.
1. A build tool should be
	1. Fast in execution
	1. Supported by a healthy community
	1. Modular & Extensible
	1. Feature-rich
1. Grunt is the most popular, but Gulp has two significant advantages over Grunt:
	1. It's build for speed and can execute tasks in parallel.
	1. Converts open files into super fast streams internally
	1. Gulp's taks, use code over configuration, which means you can just use normal JavaScript, and extend or modify tasks that don't work for you.
1. Every Gulp project starts with a Gulp file. The file sits in the root directory and define all the task that you should execute when running Gulp.
1. Most basic Gulp file
	```JavaScript
	var gulp = require('gulp');

	gulp.task('default', function() {
		// Do useful stuff here

	});
	```
1. An article about Grunt vs. Gulp - [Streams](http://jaysoo.ca/2014/01/27/gruntjs-vs-gulpjs/#streams-all-the-way-down)
1. Grunt for example, have tasks that copy your file to a temporary place where they make some change on them. As a result, every task incurs a penalty for `I/O` in the file system operations.
1. Gulp on the other hand, converts your input files into an in memory stream. The `I/O` is only done initially, and at the very end of all tasks. This is what gives Gulp such a great speed increase in many situations.
1. Write stylesheet in [Sass](http://sass-lang.com/), a super set of CSS that gets rid of many of CSS annoyances, and compile it to pure CSS.
1. Instead of prefixing CSS properties for every browser manually, you can automate with auto prefixer. [Gulp auto-prefixer](https://www.npmjs.com/package/gulp-autoprefixer)
	1. Basically another reciever of pipe stream from Sass - specifies the browser's option of autoprefixer, which tells autoprefixer for which browser versions to prefix. e.g. last two versions
	1. this auto generates `-webkit-` for you.
```
var gulp = require('gulp');
var sass = require('gulp-sass');
var autoprefixer = require('gulp-autoprefixer');

gulp.task('default', function() {
	console.log('hello world!');
});

gulp.task('styles', function() {
	gulp.src('sass/**/*.scss') // with any potential subdirectories
		.pipe(sass().on('error', sass.logError))
		.pipe(autoprefixer({
			browsers: ['last 2 versions']
		}))
		.pipe(gulp.dest('./css'));
});

``` 
1. Use Gulp Watch - think of it as an automatic trigger that waits for something to change - never have to switch to terminal again.
```
gulp.task('default', function() {
	gulp.watch('sass/**/*.scss', ['styles']); // 'folders', callback function or array with set of tasks
});
```
## Expressive Live Editing
1. Live Editing! - no need to reload browser OMG!
	1. Every keystroke in Sublime
	1. Every save via Gulp
	1. All in the browser!
1. Advantages of live editing
	1. Fewer context switches - editor and browser side by side
	1. Less clicks and keystrokes when changing code
	1. Quicker previews of changes
1. [Brackets](brackets.io) (a text editor) comes with live editing built in.
1. Sublime doesn't have a built in, but the Takana plugin gets pretty close - only supports CSS and SCSS live editing but not HTML though..
1. Chrome Dev Tools has a feature called Workspaces - allows you to work directly in the browser and changes will persist. [Link](https://developer.chrome.com/devtools/docs/workspaces) to set up Workspace. But this method isn't aware of your build process though.
1. [Browser sync](http://www.browsersync.io/) allows live editing that is assisted by our build tool. Use `watch` task to make it work. Compatible with most node.js based build tools including Gulp.
1. Linting is a way to automatically check your JS code for errors.
1. Code style linting vs. Syntax or Structural Linting
	1. Syntax or structural linting - check for JS anti-patterns, such as unreachable statements or forgetting to do a strict comparison against null
	1. Code style linting -  variables that aren't properly camel cased, or particular way of placing braces for a function.
	1. Linter only checks for potential errors, it doesn't ensure your code runs properly.
1. 3 popular JS linters: JSHint, JSCS, ESLint. [Comparison](http://www.sitepoint.com/comparison-javascript-linting-tools/)
1. ESLint supports modern ES6 code, can be extended and has output that's really easy to understand.
1. ESlint is already installed globally - to execute, cd to directory and type `eslint --init`
Questions answered:
```
? How would you like to configure ESLint? Answer questions about your style
? Are you using ECMAScript 6 features? Yes
? Are you using ES6 modules? Yes
? Where will your code run? Browser
? Do you use CommonJS? No
? Do you use JSX? No
? What style of indentation do you use? Tabs
? What quotes do you use for strings? Single
? What line endings do you use? Unix
? Do you require semicolons? Yes
? What format do you want your config file to be in? JSON
```
1. `"extends": "eslint:recommended"` in the json means you use recommended settings unless specified otherwise.
1. [Have the build run the linter](https://www.youtube.com/watch?v=cBSFMhy1upc)
1. Unit Test - JavaScript functions that pragmatically test an API or aspect of your project.
1. Unit Test in Gulp - to make Jasmine work with our build, is to use a headless browser that we can execute an instruct from the command line because that's what gulp can deal with. Such a browser exists and it's called [PhantomJS](http://phantomjs.org/). Its basically a headless version of webkit. [PhantomJS gulp plugin](https://www.npmjs.com/package/gulp-jasmine-phantom)
1. A headless browser is a web browser without graphical user interface - a browser, a piece of software that access web pages but doesn't show them to any human being. They're actually used to provide content of web pages to other programs.

## Optimisation
1. Minifying and concatenate the source files, optimizing images etc.
1. Optimisation is only meant for production.
1. Split task into **development** and **production**.
	1. Development - things you really need no matter what SASS processing for example.
	1. Always test production version from time to time.
1. `dist` forlder to holds all generated files and source files.
1. CSS concatenation - glue CSS and JS files together through concatenation then crunch them by minifying.
1. Sass does both concatenation and minification - include single Sass file in your HTML, use import directive in your Sass to input other files into your base file, when the Sass compiler processes the Sass into CSS, it will automatically inline those inputs and generate one big CSS file. A minification is an optional way, modify the Sass pipe slightly and add outputStyle: 'compressed' as config, which will produce a nicely compressed file like this:
`pipe(sass({outputStyle: 'compressed'}))`
1. Concatenation solves 2 problems:
	1. Reduces number of HTTP requests needed to load your page in production - mobile has up to 300 ms of latency per request.
	1. Most basic variant of dependency management. You put all your script into a folder and instead of having to add script blocks to load them one by one into HTML, you simply add a single script block that points to the generated output file, including all of them concatenated.
1. After concatenation - time for minification. As JS minification is a very time-intensive process, its makes no sense to do this while live-editing your code.
1.  GZIP is even more effective - it compresses the file before it gets into the browser and the browser deflates it. All this happens transparently in the background usually only requires a small server configuration change. [More info](https://css-tricks.com/the-difference-between-minification-and-gzipping/)
1. Running the latest spec of JS - ECMAScript6, eventhough there's no native browser support for many of the feature. All we need is a transpiler, which takes one programming language and converts it into another. Sometimes transpilers stay very close to ECMAScript syntax, adding in a few features here and there. In other cases they are full implementations of languages you don't typically find in purely front end web development. You can use [BabelJs](https://babeljs.io/)
	1. Things like arrow functions, generators and classes - find out in Babel.
1. Basically, using a transpiler like BabelJS gives you the chance to use tomorrow's features today without ruining user experience.
1. JavaScript Source Maps - Imagine you’re running your page now and there’s a bug in your JavaScript. so you head over to the Sources panel to set a breakpoint, only to realize you’re looking at Spaghetti instead of source code. After all the optimizations, none of your code is particularly readable anymore. That's a major rationale for source maps. Source maps are files that associate line numbers from the processed file to the original. This way the browser can lookup the current line number in the sourcemap and open the right source file at the correct line when debugging. In Chrome for instance, the DevTools support source maps both for CSS and JavaScript.
1. Setting up source map:
	1. install the gulp-sourcemaps [plugin](https://www.npmjs.com/package/gulp-sourcemaps)
	1. Require the `gulp-sourcemaps` plugin and in your scripts-dist or scripts (or styles) task, add a pipe to `sourcemaps.init()` after you get the source but before you send the source files through any pipes that transform them materially. After all plugins and pipes have been applied but before you save to the destination, pipe through `sourcemaps.write()` with an optional location parameter if you don't want the source maps to be inlined.
	Like this:
	```
	var sourcemaps = require('gulp-sourcemaps');

	 gulp.task('scripts-dist', function() {
	   gulp.src('js/**/*.js')
	    .pipe(sourcemaps.init())
	    .pipe(concat('all.js'))
	    .pipe(uglify())
	    .pipe(sourcemaps.write())
	    .pipe(gulp.dest('dist/js'));
	});	


	```
	All of the pipes between init and write must have support. Check the list [here](https://github.com/floridoo/gulp-sourcemaps/wiki/Plugins-with-gulp-sourcemaps-support) to verify. In the developer console, the output of app should automically link errors in the generated code to their line numbers in the original source.
1. 63 % of average website bytes are images.
1. Image compression notes from [udacity](file:///Users/karshenglee/Dropbox/notes/saved%20pages/Image%20Compression%20-%20Udacity.htm)
1. Try `gulp-imagemin`. [More info](https://www.npmjs.com/package/gulp-imagemin)
1. Hamburger - Lossy and SVG
1. Hero Image - Lossless
1. Image Thumbnails - Lossy

## Automating Automation
1. Scaffolding is a way of creating a starting point structure for your project based on a couple of assumptions that you control.
1. Scaffolding tools: [Web Starter Kit](https://developers.google.com/web/tools/starter-kit/), [Yeoman](http://yeoman.io/)
1. 