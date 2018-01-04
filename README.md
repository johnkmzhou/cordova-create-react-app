# Cordova Create React App Tutorial

I was dissatisfied with tutorials that didn't meet my needs, boilerplate projects with incompatible dependencies, and yeoman generators with errors so I decided to make a tutorial to help others set up their initial project. Also if you're migrating an existing web app to Cordova, this will provide insight into what you need to get that to work.

Note that I'm running this tutorial with Cordova 8 but it should work with other versions.

## Installation

Install the Create React App CLI.

`npm install -g create-react-app`

Install the Cordova CLI.

`npm install -g cordova`

Create the project.

`create-react-app tutorial`

Because we will be editing the Webpack configuration, go to your Create React App project directory and run:

`yarn run eject`

Go to your config/paths.js file and change

`appBuild: resolveApp('build')` to `appBuild: resolveApp('www')`

Because your files will be served from `file://` add this line to your package.json (https://github.com/facebookincubator/create-react-app/issues/1094):

`"homepage": "./"`

Now we will need some files from a Cordova project.

`cordova create sample com.example.tutorial Tutorial`

Next copy the config.xml from your Cordova project to your Create React App project. The other files and directories in the Cordova project are not currently used in this tutorial but take note of them because you may make use of them as your project develops.

Now we can build our output to the www directory.

`yarn run build`

The rest of these instructions have files and changes that are not in the current repository due to the nature of the dependencies that have to be brought down. Also I didn't want to tie the tutorial down with specific versions of Android and iOS.

To target platforms:

`cordova platform add ios`

`cordova platform add android`

You need to install SDKs for each platform that you wish to target. Read this to check what requirements need to be satisfied: https://cordova.apache.org/docs/en/latest/guide/cli/index.html#install-pre-requisites-for-building
Generally you will probably have to install Android Studio, XCode, SDKs, emulators, build systems, etc.

To test the app run:

`cordova emulate android`

`cordova emulate ios`
