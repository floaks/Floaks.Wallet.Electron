# Floaks.Wallet Desktop App [![Build Status](https://img.shields.io/travis/Floaks/Floaks.Wallet.Electron/master.svg)](https://travis-ci.org/Floaks/Floaks.Wallet.Electron) [![Build status](https://ci.appveyor.com/api/projects/status/k72eq3gm42wt4j8b?svg=true)](https://ci.appveyor.com/project/Floaks/floaks-wallet-electron) [![Project Dependencies](https://david-dm.org/Floaks/Floaks.Wallet.Electron.svg)](https://david-dm.org/Floaks/Floaks.Wallet.Electron) [![Codacy Badge](https://api.codacy.com/project/badge/Grade/54ebf39732d14cb19a1a992b46bd0da6)](https://www.codacy.com/app/Floaks/Floaks-Wallet-Electron?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=Floaks/Floaks.Wallet.Electron&amp;utm_campaign=Badge_Grade)

Desktop application for [Floaks.Wallet](https://github.com/floaks/Floaks.Wallet) available for macOS, Windows and Linux using [Electron](http://electron.atom.io).

# Download

You can download the latest version from the [Releases](https://github.com/floaks/Floaks.Wallet.Electron/releases/latest) page.

# Install
Launch the installer and follow the instructions to install.

## Windows Options
On Windows you can run a silent install by adding the `/S` flag. You can also add the options below:

- `/S` - Silent install
- `/allusers` - Install for all users (requires admin)
- `/currentuser` - Install for current user only (default)

# Development

## Quick start

Prerequisites:

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org)
* [node-gyp](https://github.com/nodejs/node-gyp#installation)
* [Yarn](http://yarnpkg.com/) is recommended instead of npm.

Now just clone and start the app:

```sh
git clone https://github.com/floaks/Floaks.Wallet.Electron.git
cd Floaks.Wallet.Electron
yarn
yarn start
```

## Structure of the project

The sources is located in the `src` folder. Everything in this folder will be built automatically when running the app with `yarn start`.

Stylesheets are written in `less` and are located in `src/stylesheets`. They will be build into a single `main.css` in the `app` folder.

The build process compiles all stuff from the `src` folder and puts it into the `app` folder, so after the build has finished, your `app` folder contains the full, runnable application.

## The build pipeline

Build process is founded upon [gulp](https://github.com/gulpjs/gulp) task runner and [rollup](https://github.com/rollup/rollup) bundler. There are two entry files for your code: `src/background.js` and `src/app.js`. Rollup will follow all `import` statements starting from those files and compile code of the whole dependency tree into one `.js` file for each entry point.


## Adding node modules

Remember to respect the split between `dependencies` and `devDependencies` in `package.json` file. Only modules listed in `dependencies` will be included into distributable app.

Side note: If the module you want to use in your app is a native one (not pure JavaScript but compiled C code or something) you should first  run `yarn add name_of_module` and then `yarn postinstall` to rebuild the module for Electron. This needs to be done only once when you're first time installing the module. Later on postinstall script will fire automatically with every `yarn install`.

## Working with modules

Thanks to [rollup](https://github.com/rollup/rollup) you can (and should) use ES6 modules for most code in `src` folder.

Use ES6 syntax in the `src` folder like this:
```js
import myStuff from './my_lib/my_stuff';
```

The exception is in `src/public`. ES6 will work inside this folder, but it is limited to what Electron/Chromium supports. The key thing to note is that you cannot use `import` and `export` statements. Imports and exports need to be done using CommonJS syntax:

```js
const myStuff = require('./my_lib/my_stuff');
const { myFunction } =  require('./App');
```

## Issues with Install

### node-gyp
Follow the installation instruction on [node-gyp readme](https://github.com/nodejs/node-gyp#installation).

#### Ubuntu Install
You will need to install:
```sh
build-essential
libevas-dev
libxss-dev
```
### Fedora Install
You will need to install:
```sh
libX11
libXScrnSaver-devel
gcc-c++
```

#### Windows 7
On Windows 7 you may have to follow option 2 of the [node-gyp install guide](https://github.com/nodejs/node-gyp#installation) and install Visual Studio

# Testing

## Unit tests

```
yarn test
```

Using [electron-mocha](https://github.com/jprichardson/electron-mocha) test runner with the [chai](http://chaijs.com/api/assert/) assertion library. This task searches for all files in `src` directory which respect pattern `*.spec.js`.

## End to end tests

```
yarn e2e
```

Using [mocha](https://mochajs.org/) test runner and [spectron](http://electron.atom.io/spectron/). This task searches for all files in `e2e` directory which respect pattern `*.e2e.js`.

## Code coverage

```
yarn coverage
```

Using [istanbul](http://gotwarlost.github.io/istanbul/) code coverage tool.

You can set the reporter(s) by setting `ISTANBUL_REPORTERS` environment variable (defaults to `text-summary` and `html`). The report directory can be set with `ISTANBUL_REPORT_DIR` (defaults to `coverage`).

# Making a release

To package your app into an installer use command:

```
yarn release
```

It will start the packaging process for operating system you are running this command on. Ready for distribution file will be outputted to `dist` directory.

Right now you can only create Windows installer when running Windows, the same is true for macOS. For Linux builds, you can use our [Docker image](https://hub.docker.com/r/floakswallet/electron.builder/) with the following commands:
```
docker run --rm -ti -v ${PWD}:/project -v ${PWD##*/}-node-modules:/project/node_modules -v ~/.electron:/root/.electron floakswallet/electron.builder /bin/bash -l -c "yarn && yarn release -- --x64 --ia32 --p never"
```

All packaging actions are handled by [electron-builder](https://github.com/electron-userland/electron-builder). It has a lot of [customization options](https://github.com/electron-userland/electron-builder/wiki/Options), which you can declare under ["build" key in package.json file](https://github.com/szwacz/electron-boilerplate/blob/master/package.json#L2).

# Default servers

The `servers.json` file will define what servers the client will connect to and will populate the server list in the sidebar, it contains a list of default servers which will be added the first time the user runs the app (or when all servers are removed from the list).
The file syntax is as follows:
```
{
  "Demo Floaks Wallet": "https://demo.floaks.com",
  "Open Floaks Wallet": "https://open.floaks.com"
}
```

## Pre-Release Configuration

You can bundle a `servers.json` with the install package, the file should be located in the root of the project application (same level as the `package.json`). If the file is found, the initial "Connect to server" screen will be skipped and it will attempt to connect to the first server in the array that has been defined and drop the user right at the login screen. Note that the `servers.json` will only be checked if no other servers have already be added, even if you uninstall the app without removing older preferences, it will not be triggered again.

## Post-Install Configuration

If you can't (or don't want to) bundle the file inside the app, you can create a `servers.json` in the user preferences folder which will overwrite the packaged one. The file should be located in the `%APPDATA%/Floaks.Wallet+/` folder or the installation folder in case of a installation for all users (Windows only).

For Windows the full paths are:
```
~\Users\<username>\AppData\Roaming\Floaks.Wallet+\
~\Program Files\Floaks.Wallet+\Resources\
```
On MacOS the full path is:
```		
~/Users/<username>/Library/Application Support/Floaks.Wallet+/
~/Applications/Floaks.Wallet+.app/Contents/Resources/
```
On Linux the full path is:
```
/home/<username>/.config/Floaks.Wallet+/
/opt/Floaks.Wallet+/resources/
```

# Useful links

http://developerthing.blogspot.com.br/2017/01/awesome-electron.html

# License

Released under the MIT license.