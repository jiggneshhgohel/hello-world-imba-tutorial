In this part we will work on introducing [webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server) into our application.

Its documentation says

> Use [webpack](https://webpack.js.org/) with a development server that provides live reloading. This should be used for development only.

> It uses [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware) under the hood, which provides fast in-memory access to the webpack assets.

Now an obvious question which might arise is that why do we need webpack-dev-server when our purpose with the application is already achieved in terms that everything is working as expected. To answer that consider following:

* Presently our server is built on top of `express` and as was mentioned in earlier part of this tutorial, it just serves static files. So our application is not using its real potential in terms of it being used as a web-application framework. We are using it just for serving static files.

* Another thing is that everytime we make any changes to our frontend code in `project_root/src/frontend/client.imba` we have to rebuild the bundle using `npm run build` for making the changes come into effect on the browser and at times we might also need to restart the server using `npm run start` in case just rebuilding the bundle doesn't help.
So this frequent rebuilding of bundle, post any changes in our frontend code, becomes a tedious task as the application grows. So how we can avoid this? How about if we had some solution which continuosly watches our frontend code and as soon as we change it, the former rebuilds the package and shows the updated changes in browser automatically? Yes that sounds like live reloading and webpack-dev-server is built to solve the problem we just discussed. So we have two things:

  * watch the code changes

  * live reload the changes in browser

**watch** can be leveraged by using [webpack's watch mode](https://webpack.js.org/guides/development/#using-watch-mode) and **live reload** by [webpack-dev-server](https://webpack.js.org/guides/development/#using-webpack-dev-server). webpack-dev-server also serves the static assets as was mentioned earlier in a quote from
package's documentation page.

OK now that the intention is made clear behind using the webpack-dev-server let's work onto introduce it in our application.

So we start by installing webpack-dev-server.

```
project_root$ npm install webpack-dev-server --save
```

That should do 2 things like we have explained earlier with installation of other packages i.e. it would update `dependencies` section in `package.json` and update the `package-lock.json` accordingly.

Next we remove `express` from our application.

```
project_root$ npm uninstall express
```

That again should do 2 things i.e. it would update `dependencies` section in `package.json` and update the `package-lock.json` accordingly.

The removal of `express` from our application also makes the `project_root/src/backend/server.imba` redundant. So let's remove it too.

```
project_root$ rm -r src/backend
```

Next to start webpack-dev-server we update our `package.json` in following manner:

* Replace "scripts" entry with following

```
  "scripts": {
    "build": "webpack",
    "watch": "webpack --watch",
    "start:dev": "webpack-dev-server"
  }
```

That's as per what is shown at https://webpack.js.org/guides/development/#using-webpack-dev-server. And to use that one should run following command
to enable watching

```
project_root$ npm run watch
```

That is as shown under documentation at https://webpack.js.org/guides/development/#using-watch-mode


and in another terminal you should run following command to start webpack-dev-server.

```
project_root$ npm run start:dev
```

But if you check `watch` config options' [documentation](https://webpack.js.org/configuration/watch/#watch) it says following:

> In webpack-dev-server and webpack-dev-middleware watch mode is enabled by default.

So `"watch": "webpack --watch"` in our `scripts` is redundant and hence we should remove it. After removal `scripts` should look like

```
  "scripts": {
    "build": "webpack",
    "start:dev": "webpack-dev-server"
  }
```

Now you can verify that without the explict watch script the watch is enabled. To do that run

```
project_root$ npm run start:dev
```

That should start the server and also compile the files. You should see output like following:

```
> hello-world-imba@1.0.0 start:dev <project_root>
> webpack-dev-server

ℹ ｢wds｣: Project is running at http://localhost:8080/
ℹ ｢wds｣: webpack output is served from /
ℹ ｢wdm｣: Hash: 536c074b2d6f31d8c77d
Version: webpack 4.29.0
Time: 723ms
Built at: 01/27/2019 4:49:22 PM
    Asset     Size  Chunks             Chunk Names
client.js  450 KiB     app  [emitted]  app
Entrypoint app = client.js
[0] multi (webpack)-dev-server/client?http://localhost:8080 ./src/frontend/client.imba 40 bytes {app} [built]
[./node_modules/ansi-html/index.js] 4.16 KiB {app} [built]
[./node_modules/events/events.js] 13.3 KiB {app} [built]
[./node_modules/html-entities/index.js] 231 bytes {app} [built]
[./node_modules/imba/imba.imba] 51 bytes {app} [built]
[./node_modules/imba/src/imba/index.imba] 574 bytes {app} [built]
[./node_modules/loglevel/lib/loglevel.js] 7.68 KiB {app} [built]
[./node_modules/url/url.js] 22.8 KiB {app} [built]
[./node_modules/webpack-dev-server/client/index.js?http://localhost:8080] (webpack)-dev-server/client?http://localhost:8080 7.78 KiB {app} [built]
[./node_modules/webpack-dev-server/client/overlay.js] (webpack)-dev-server/client/overlay.js 3.58 KiB {app} [built]
[./node_modules/webpack-dev-server/client/socket.js] (webpack)-dev-server/client/socket.js 1.05 KiB {app} [built]
[./node_modules/webpack-dev-server/node_modules/strip-ansi/index.js] (webpack)-dev-server/node_modules/strip-ansi/index.js 161 bytes {app} [built]
[./node_modules/webpack/hot sync ^\.\/log$] (webpack)/hot sync nonrecursive ^\.\/log$ 170 bytes {app} [built]
[./node_modules/webpack/hot/emitter.js] (webpack)/hot/emitter.js 75 bytes {app} [built]
[./src/frontend/client.imba] 313 bytes {app} [built]
    + 24 hidden modules
ℹ ｢wdm｣: Compiled successfully.
```


Now make any changes to any file say `project_root/src/frontend/client.imba` and as soon as you save those
changes you should see again see the logs like following.


```
ℹ ｢wdm｣: Compiling...
ℹ ｢wdm｣: Hash: b7f1d1366cbd4fdc956c
Version: webpack 4.29.0
Time: 21ms
Built at: 01/27/2019 5:53:51 PM
    Asset     Size  Chunks             Chunk Names
client.js  450 KiB     app  [emitted]  app
Entrypoint app = client.js
[./src/frontend/client.imba] 313 bytes {app} [built]
    + 38 hidden modules
ℹ ｢wdm｣: Compiled successfully.

```


That implies watch is working! So that is pretty much about watch in general and in context of webpack-dev-server.

^^^^^^^^^^^^^^^^^

An important thing to note is that even though watch is enabled you would initially need to run following to build the bundle


```
project_root$ npm run build
```

and on subsequent changes in files the bundle would automatically get updated. How avoiding this affects us can be verified by manually removing the file
`client.js` from `project_root/dist` and then run `npm run start:dev` and you would not find the `client.js` being generated in folder `project_root/dist`.

^^^^^^^^^^^^^^^^^

Now that webpack-dev-server is running, open a browser window at http://localhost:8080 and you should see your `Hello, World!` in the browser page.

**Wait! It doesn't show `Hello, World!`!!! It shows our application's files and directories listing.**

OK to fix that we should go back to the `npm run start:dev` command output, when it was first ran, to see if we can get any clue on why this happened? If you notice we can see a line there

```
ℹ ｢wds｣: webpack output is served from /
```

The `/` implies our that webpack-dev-server is serving files from our `project_root`. But we want to serve the files from `project_root/dist` folder because that is where
our `index.html` lies and also the bundled `client.js`. So we should need to instruct `webpack-dev-server` to serve files from `project_root/dist`. So how we do that? Now if
you check the package's documentation at https://www.npmjs.com/package/webpack-dev-server#the-result there is a paragraph referencing a link to the documentation

> See the [documentation](https://webpack.js.org/configuration/dev-server/#devserver) for more use cases and options.

Let's open that link and see what we find there.

We can see a [devServer](https://webpack.js.org/configuration/dev-server/#devserver) option which looks like it can help us resolve the problem we are facing. Let's introduce
that option in our `webpack.config.js` by adding following:

```
  devServer: {
    contentBase: path.resolve(__dirname, 'dist')
  }
```

After adding it should look like

```
...

module.exports = {
    ...
    ...
    devServer: {
        contentBase: path.resolve(__dirname, 'dist')
    }
}
```

Now stop and restart the server

```
project_root$ npm run start:dev
```

This time you should see an following additional line in the output in addition to the output previously shown for this command

```
ℹ ｢wds｣: Content not from webpack is served from $project_root$/dist
```

Now open a browser window at http://localhost:8080 and you should see your `Hello, World!` in the browser page.

Now edit the code in `project_root/src/frontend/client.imba` by changing

```
  <p> "Hello, World!"
```

TO

```
  <p> "Hello, World! Live reloaded!"
```

and you should see this change automatically in effect in browser page at http://localhost:8080. Congrats!

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Now one might wonder like earlier with express-based server that we didn't explicitly requested any file then how come the `index.html` was served? To answer that
webpack has a configuration option [devServer.index](https://webpack.js.org/configuration/dev-server/#devserver-index) which defaults to value `index.html`. How is that because in documentation it is not specified? To answer that you should refer https://github.com/webpack/webpack-dev-middleware#index option's default value and as has been mentioned earlier `webpack-dev-server`uses `webpack-dev-middleware` under the hood. For curious people like me I figured that out from [webpack-dev-server code](https://github.com/webpack/webpack-dev-server/blob/master/lib/Server.js#L471) and if you search for `middleware` in that file you will find `this.middleware` set to `webpackDevMiddleware` at [line](https://github.com/webpack/webpack-dev-server/blob/master/lib/Server.js#L166)**


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Now one minor change: If you check the [documentation](https://webpack.js.org/configuration/dev-server/#devserver-contentbase) for `devServer.contentBase` option you can see that
there is following section

> Usage via the CLI

which shows how the `contentBase` option can be specified in command-line. To use it from command-line we should two things:

1. Remove following config option we added to our `webpack.config.js`

```
    devServer: {
        contentBase: path.resolve(__dirname, 'dist')
    }
```

2. Update `package.json` by updating `scripts` section in following manner:

```
  "scripts": {
    "build": "webpack",
    "start:dev": "webpack-dev-server --content-base dist"
  }
```

**Note that this `path` specified is relative to our `project_root`.**

So finally we are done with this. Now save the changes made in codebase and commit the changes.
