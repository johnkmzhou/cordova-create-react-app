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

This is specific to create-react-app. In other projects you would need to ensure your paths are not prepended with a /.

Now we will need some files from a Cordova project.

`cordova create tutorial com.example.tutorial Tutorial`

Next copy the config.xml from your Cordova project to your Create React App project. The other files and directories in the Cordova project are not currently used in this tutorial but take note of them because you may make use of them as your project develops. For example, the `res` directory would be where you would place icons and splash screens.

Next modify your index.js so it looks like:
```
const startApp = () => {
  ReactDOM.render(<App />, document.getElementById('root'));
  registerServiceWorker();
};

if(window.cordova) {
  document.addEventListener('deviceready', startApp, false);
} else {
  startApp();
}
```

And add `<script type="text/javascript" src="cordova.js"></script>` to index.html. You may also want to add
```
<meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: https://ssl.gstatic.com 'unsafe-eval'; style-src 'self' 'unsafe-inline'; media-src *; img-src 'self' data: content:;">
<meta name="format-detection" content="telephone=no">
<meta name="msapplication-tap-highlight" content="no">
```

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

To test on a connected devie run:

`cordova run android`

`cordova run ios`

## Recommended Plugins

Often what you will find is that a plugin is no longer being maintained. In that scenario your options are either to find a fork that resolves the issues you are encountering or, as a last ditch effort, attempt to fix it yourself.

### Firebase OAuth Authentication

To authenticate using OAuth Providers follow these instructions: https://firebase.google.com/docs/auth/web/cordova

Because the cordova-universal-links-plugin is outdated you should instead install this fork:
```
cordova plugin add https://github.com/walteram/cordova-universal-links-plugin.git --save
```

### Firebase Analytics and Push Notifications

Due to various issues that haven't beeen resolved in the latest npm package of cordova-plugin-firebase I recommend getting the version of the plugin directly from git
```
cordova plugin add https://github.com/arnesson/cordova-plugin-firebase.git --save
```

## iOS Deployment

First, sign up for a paid Apple Developer Account. In Xcode, open your project from `/platforms/ios` and go to your Preferences > Accounts. Add the Apple ID that was used for the developer account. Select your Agent Role and click on `Manage Certificates..` and click on the `+` icon to generate a signing certificate. 

Next, go to `https://developer.apple.com/account/ios/profile` and add a new provisioning profile. Select your development or distribution option and on the next page fill your App ID Description (for example, My Cordova App) and your bundle ID that can be found in config.xml.

Next, in Xcode go to your project navigator and select your project. In a drop down list make sure your project is selected as a target. Then, under Signing, select your Team and generate your signing certificate.

To prepare for distribution on the Apple App Store, you need to create an archive. In Xcode make sure the app is properly provisioned and signed for distribution. Next run `yarn build` followed by `cordova prepare` to copy any assets and plugins. Then in Xcode, ensure the version and build numbers are properly set and choose a generic device target from the Scheme toolbar menu. Then choose Product > Archive. There is an issue uploading to the App Store from the Archives window. Instead export the archive and upload it via Application Loader. After uploading the build to iTunes Connect, if the build is still in the "Processing" phase after an hour, attempt to upload another build because more than likely there's probably an issue with Apple's servers.

## Android Deployment

First, sign up for a Google Play Developer account. Next, run `yarn build` followed by `cordova build android --release` which will result in an apk that needs to be signed. Then run: 
```
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore android.keystore app-release-unsigned.apk alias-name
```
where `android.keystore` is the location of the keystore and `alias-name` is the alias for your key. Make sure you have Android Studio installed because it will also come with the zipalign tool. For Mac OS you can find it in `~/Library/Android/sdk/build-tools/{version}/zipalign`. Then run:
```
~/Library/Android/sdk/build-tools/{version}/zipalign -v 4 app-release-unsigned.apk release.apk
```
followed by uploading the build to the Google Play Console.