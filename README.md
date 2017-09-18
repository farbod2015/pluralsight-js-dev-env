# pluralsight-js-dev-env

What is the purpose of the starter kit? How create them?

Steps:
	1- install git from "git-scm.com"
	2- sign up for a github account (at github.com)
	3- create a repository on github (select "add .gitignore: Node" to avoid checking in the Node folder)
	4- clone the repository to your computer:
		- click the "clone or download" green button and copy the URL
		- in command prompt: git clone https://github.com/farbod2015/pluralsight-js-dev-env.git
	
Helpful git commands:
	1- stage all the files you changed:
		git add .
	2- commit your changes locally:
		git commit -m "added new feature"
	3- push your work to Github:
		git push

Editors:
	1- Atom, WebStorm, Brackets, VSCode
	2- Create .editorconfig in the root consistence spacing, character set, etc. regardless of the editor
		- download the plugin for VS Code frome editorconfig.org
	
Package Management:
	1- You need a Package Manager because every language benefits from a standardized method for sharing code
	2- NPM: most popular now
	3- install Node.js
		- Node.js is an open source server framework.
		- Node.js allows you to run JavaScript on the server.
		- when you install node, npm, node's package manager comes bundled along with it

	4- package.json: 
		- is the manifest that NPM uses
		- It stores the list of npm packages that we are using as well as the npm scripts (download from bit.ly/jsdevpackagejson)
	5- to install the packages in package.json do "npm install" in command line
	
	Note:
		npm install (in package directory, no arguments):
		Install the dependencies in the local node_modules folder.
		In global mode (ie, with -g or --global appended to the command), it installs the current package context (ie, the current working directory) as a global package.
		By default, npm install will install all modules listed as dependencies in package.json.
		With the --production flag (or when the NODE_ENV environment variable is set to production), npm will not install modules listed in devDependencies.

Package Security:
	1- Packages can be published npm by anyone. "Reire.js" and the "Node Security Platform" (preferred at this time) are two ways that you can check your project's dependencies for known vulnerabilities
	2- Install Node Security Platform to automate security checks for our dependencies
		-  to run nsp directly on the command line, install it globally
		- npm install -g nsp
		- we can call this from an npm script and thus avoid having to install it globally (we'll do later)
	3- to check for vulnerabilities, in command line: nsp check
	
Development Webservers:
	1- web server can be configuered to start automatically as part of our development process
	2- it opens the application so we can view our results immediately every time that we hit save.
	3- Development Webservers: 
		- http-server
		- live-server
		- Express (we use in this course)
		- budo
		- webpack dev server
		- Browsersync
	4- express is already installed as part of package.json
	5- create buildScripts folder for all build tools
	6- create srcServer.js inside buildScripts folder
		- this file will configure a web server that will serve up the files in our source directory
	7- 
	
Sharing Work-in-progress with Customer:	
	1- tools: localtunnel, ngrok, Surge, now
	2- example: 
		- install: npm install localtunnel -git
		- node buildScripts/srcServer.js
		- in a new terminal: lt --port 3000
		- copy the URL and give it to the customer
	
Automation:
	1- Popular tools:
		- Grunt
		- Gulp
		- npm Scripts (we use this)
	2- add npm scripts to the "scripts" section of package.json:
		- "start": "node buildScripts/srcServer" --> use "npm start" to run this (use -s flag to silent the noise)
		- generally if there is "script" (e.g. "start"), you can define "prescript" (e.g. "prestart") and "postscript" (e.g. "poststart") that automatically will run before "script"
		- type "npm run scriptname" to run a script. start and test don't need the keyword "run"
		-  don't need to install packages globally if run them by npm scripts. for example we won't need to install nsp globally if we write an scipt for running it (e.g. "security-check": "nsp check"). 
		- the reason is that all packages are installed in node_modules/.bin which is in path when we call them from npm
	3- use npm-run-all package to run multiple scripts at the same time:
		- example:
			"open:src": "node buildScripts/srcServer.js",
			"security-check": "nsp check",
			"start": " npm-run-all --parallel security-check open:src",
			
Transpiling:
	1- Since we've decided to use the latest version of JavaScript in this project we need to transpile down to ES5 to assure that it runs in environments that don't yet fully support the latest versions of JavaScript. 
	2- Popular transpilers:
		- Babel: transpiles the latest version of JavaScript down to ES5 so that you can use all of these new features, but run them everywhere that ES5 is supported. But it actually does a lot more than that. Babel plugins are responsible for converting JSX (inline HTML in JavaScript, made popular by React) into plain old Javascript in the browser. 
		- TypeScript
		- Elm
	3- can configuer babel in .babelrc or package.json files.
		- add .babelrc to root (use babel-node instead of node to run the code):
			{
				"presets": [
					"latest"
				]
			}
	
Bundling:
	1- Bundlers take all your JavaScript files, and intelligently package them for a target environment, such as a browser or node, and some bundlers add a variety of additional features on top of that.
	2- Bundlers to choose from:
		- Browserify
		- Webpack
		- Rollup
		- JSPM
	3- we use Webpack because:
		- much more than just JS (CSS, images, fonts, html)
		- bundle splitting
		- hot module reloading (depending on the library or framework that you are using)
		- tree shaking: dead-code elimination (webpack 2 offers this)
	
	Note:
		- React hot reloading: As we know in Meteor, every time we save, Meteor rebundles your entire app and reloads the page, and this takes a bit of time depending on the size of your project. React hotloading is slightly different, it leverages HMR (see below) and without reloading the page, replaces changed components in-place, preserving state. That means that, your change will be visible in about half a second; if it was a dialog, you wouldn't need to re-open it, your text inputs remain filled, etc. In short, it's a massive boon to iterative development where you spend much less time waiting for your app to reload. 

		- Code splitting: As we know in Meteor, the client loads all client files on the first load. E.g. not admins will still be served all the admin templates, which will never be used, and this creates a slower load time. With code splitting, you can divide your app into different pieces. You could, e.g. load the main code straight away, and load code for particular sections if a user visits them (or load everything else after the initial load is complete and the user can start using your app).
		
	Steps:	
		1- create webpack.config.dev.js in root and export an object for configuration
		2- configure a dev server (we used webpack with express in srcServer.js for this)
		3- configure the build to automatically generate sourcemaps for debuging as part of the bundling process
			- it is set as devtool in webpack
			- there are many different options depending on the quality and speed that you are looking (we used "inline-source-map").
			- use "debugger;" as breakpoint in the javascript code. the inspector in the developer tool of the browser will show the original js code instead the bundled
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	








React hot reloading: As we know in Meteor, every time we save, Meteor rebundles your entire app and reloads the page, and this takes a bit of time depending on the size of your project. React hotloading is slightly different, it leverages HMR (see below) and without reloading the page, replaces changed components in-place, preserving state. That means that, your change will be visible in about half a second; if it was a dialog, you wouldn't need to re-open it, your text inputs remain filled, etc. In short, it's a massive boon to iterative development where you spend much less time waiting for your app to reload. 

Code splitting: As we know in Meteor, the client loads all client files on the first load. E.g. not admins will still be served all the admin templates, which will never be used, and this creates a slower load time. With code splitting, you can divide your app into different pieces. You could, e.g. load the main code straight away, and load code for particular sections if a user visits them (or load everything else after the initial load is complete and the user can start using your app).
