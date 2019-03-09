In this part we will work on introducing a Webpack Plugin which should facilitate bundling our CSS in a separate file, unlike bundling and getting it injected into `<style>` tag which was the case in the previous part of the tutorial.

An easy to follow reasoning for doing above is quoted below, with slight modifications, from [this](https://medium.com/a-beginners-guide-for-webpack-2/extract-text-plugin-668e7cd5f551) reference:

> getting the CSS files bundled and then getting the resulting css included within the <style> tags is okay at the point of the beginning of the development phase when we have very little css, but as our CSS will grow, it would be nice to have our CSS generated in a separate text file like app.bundle.css instead of having all the CSS getting injected as a string within the <style> tags in the html file.

To deal with that we should be using a Webpack [Plugin](https://webpack.js.org/concepts/#plugins). An easy to understand explanation of Plugin concept is quoted below from [this](https://medium.com/@paul.allies/webpack-managing-javascript-and-css-dependencies-3b4913f49c58) reference:

> Plugin is a process applied on your bundle before it is sent to the output file. A typical plugin is the minification plugin.

Note that the references linked here demonstrates, dealing with the above mentioned concern, using [Extract Text Plugin](https://www.npmjs.com/package/extract-text-webpack-plugin). However on the plugin's [Homepage](https://github.com/webpack-contrib/extract-text-webpack-plugin#usage) following warning is issued

> Since webpack v4 the extract-text-webpack-plugin should not be used for css. Use [mini-css-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin) instead.

Since we are using webpack v4 in our application we will use the suggested **mini-css-extract-plugin**.

OK so let's start by installing it

```
project_root$ npm install mini-css-extract-plugin --save-dev
```

That should do following 2 things:

1. update package.json by updating `devDependencies` section under it by appending it `mini-css-extract-plugin` package like following

```
  "mini-css-extract-plugin": "^0.5.0",
```

_Note: The dependencies versions are the then versions when the dependencies were installed at on this tutorial author's local machine._

2. update `package-lock.json` w.r.t changes in `package.json`

Next we will update our `webpack.config.js` by adding configuration for this plugin. To do that open the file in a text-editor and do following:

1. add following at the top of file BEFORE `module.exports`:

```
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
```

2. add following under `module.exports`

```
plugins: [
  new MiniCssExtractPlugin()
],
```

3. replace following under `module.rules` array

```
{
    use:['style-loader','css-loader'],
    test:/\.css$/
}
```

WITH

```
{
    test:/\.css$/,
    use: [
      MiniCssExtractPlugin.loader,
      "css-loader"
    ]
}
```

That is similar to how we specified `style-loader` and `css-loader` earlier. The only difference here is that here we have replaced `style-loader` with `MiniCssExtractPlugin.loader`. And that tells webpack that for any file which has filename matching with the regular expression specified in the `test` property, first use `css-loader` to compile those and then use `MiniCssExtractPlugin.loader` on the output of `css-loader`.

So after those modifications our `webpack.config.js` should look like (note unchanged parts are shown as truncated with `...`)


```
....
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
    ...,
    ...,
    plugins: [
      new MiniCssExtractPlugin()
    ],
    module: {
        rules: [
            ...,
            {
                test:/\.css$/,
                use: [
                  MiniCssExtractPlugin.loader,
                  "css-loader"
                ]
            }
        ]
    },
    ...,
    ...
}
```

Note that we didn't used the Minimal Config as shown [here](https://github.com/webpack-contrib/mini-css-extract-plugin#minimal-example). That is we didn't provided
following options while instantiating `MiniCssExtractPlugin`

```
filename: "[name].css",
chunkFilename: "[id].css"
```

neither we specified following loader options

```
options: {
  // you can specify a publicPath here
  // by default it use publicPath in webpackOptions.output
  publicPath: '../'
}
```

**That's because we don't need such customizations at this point and the default values for those options should work for us.**

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

BTW if you are wondering what does `[name]`, `[id]` etc mean then you should refer the official [documentation](https://webpack.js.org/configuration/output/#output-filename).
In a nutshell they are placeholders used to attach specific information to webpack output. Additional references I found are listed below:

* https://survivejs.com/webpack/optimizing/adding-hashes-to-filenames/
* https://stackoverflow.com/q/50202837/936494


^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Now let's re-build the bundle using following command


```
project_root$ npm run build
```

That should show output like following

```
> hello-world-imba@1.1.0 build $project_root$
> webpack

Hash: 101c2d4b5d4bf36e9417
Version: webpack 4.29.0
Time: 697ms
Built at: 02/08/2019 2:26:38 AM
    Asset      Size  Chunks             Chunk Names
  app.css  22.9 KiB     app  [emitted]  app
client.js   108 KiB     app  [emitted]  app
Entrypoint app = app.css client.js
[./node_modules/webpack/buildin/global.js] (webpack)/buildin/global.js 472 bytes {app} [built]
[./src/css/w3.css] 39 bytes {app} [built]
[./src/frontend/client.imba] 872 bytes {app} [built]
    + 14 hidden modules
Child mini-css-extract-plugin node_modules/css-loader/dist/cjs.js!src/css/w3.css:
    Entrypoint mini-css-extract-plugin = *
    [./node_modules/css-loader/dist/cjs.js!./src/css/w3.css] 23.5 KiB {mini-css-extract-plugin} [built]
        + 1 hidden module
```

As can be seen there are two assets generated by names `client.js`, `app.css` both under `project_root/dist` folder as specified by our `output` property
in `webpack.config.js`. The `app.css` has been generated by the `mini-css-extract-plugin` we configured. *The file name and the output folder to generate to etc
should be based on default values used by it.*

Now let's start the webpack-dev-server using command

```
project_root$ npm run start:dev
```

and then open a browser window at http://localhost:8080 and you should see the styled content.

**Oops! It shows content but no styles are getting applied.**

OK let's open the Browser's Developer Tools, for e.g. in Firefox `Inspector` tab and check the DOM source for whether we have our generated `app.css` referenced there. No! So the obvious reason for no styles getting applied is the page missing reference to our bundled CSS. If you recollect earlier we used `style-loader` to inject the bundled CSS
into `<style>` tag under `<head>` but with `mini-css-extract-plugin` we wanted the CSS to be bundled in a separate file. Now `mini-css-extract-plugin` just generates this separate bundle and doesn't do automatic injection of bundle reference into DOM. So we should manually reference it like we did initially, when we introduced CSS into our application, using `<link>` tag, which we removed later while introducing `css-loader` and `style-loader`.

So let's add the `<link>` tag again. To do that open up `project_root/dist/index.html` in a text editor and add the following `<link>` tag under the `<head>` tag

```
<link rel="stylesheet" href="app.css">
```

Since our webpack-dev-server is configured to serve content from `project_root/dist` folder the `app.css` file path doesn't need to be specified as `dist/app.css`. *To recollect refer the explanation of referencing file paths served by webpack-dev-server from a particular directory in the section where we introduced CSS in our application.*

Now try refreshing the page in browser. Bingo! The styling is restored. Congrats!

Now one last thing remains is to update our `.gitignore` file by adding `dist/app.css` because that is a bundled asset which is not intended to be pushed to our repository.

Now save the changes made in codebase and commit the changes.
