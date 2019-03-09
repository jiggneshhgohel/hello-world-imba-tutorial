In this part we will work on introducing CSS into our application for styling our page.

For this application we would use [W3.CSS](https://www.w3schools.com/w3css/default.asp). Its's documentation says

> W3.CSS is a modern CSS framework with built-in responsiveness. It supports responsive mobile first design by default, and it is smaller and faster than similar CSS frameworks.

> W3.CSS can also speed up and simplify web development because it is easier to learn, and easier to use than other CSS frameworks

So using W3.CSS we can avoid writing the boilerplate for standard styles needed in a typical web application.

OK so let's start.

First download the W3.CSS from the official [source](https://www.w3schools.com/w3css/default.asp).

Next create a folder `css` under `project_root/src` and copy the downloaded W3.CSS to it.

Next open up `project_root/dist/index.html` in an editor and add following content under the `<head>` tag in it:

```
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="src/css/w3.css">
```

Next open up `project_root/src/frontend/client.imba` in an editor and replace the code under `<self>` with following:

```
<div.w3-container.w3-teal>
    <h1> "Hello, World!"

<div.w3-container>
    <p>
        "This is the traditional Hello World application built using "
        <a href="http://imba.io" target="_blank"> "Imba"
        <span> "."

<div.w3-container.w3-teal>
    <p> "Footer"
```

_**Note that Imba is indentation-based so inconsistent indentation would cause Imba compiler to raise errors**_


Now re-build the bundle using following command, in case you made the above change prior running `npm run start:dev`.


```
project_root$ npm run build
```

*If the webpack-dev-server was first started and then the changes were made that should have caused Webpack to automatically rebuild the bundle. To recollect refer the details above regarding watch and live-reloading explained in earlier part of the tutorial.*

Assuming webpack-dev-server is not running start it using command

```
project_root$ npm run start:dev
```

and then open a browser window at http://localhost:8080 and you should see the the content added in `client.imba` to be rendered as styled with the W3.CSS stylings we used.

**Wait! It shows updated content but no styles are getting applied.**

OK to find out the root cause behind this problem we should look into Browser's Developer Tools. For e.g. in Firefox `Inspector` tab I can see the DOM source.
There in we need to look into whether the `src/css/w3.css` path is resolved and whether the stylesheet is loaded. Checking that we should see that the stylesheet
is not loaded and hence the stylings weren't applied to markup. To find out the cause behind it not getting resolved try accessing the CSS file from the browser by
typing in `http://localhost:8080/src/css/w3.css` in the address bar and you should see following message

> Cannot GET /src/css/w3.css

So the root cause is that the CSS file path is not getting resolved. Now if you recollect we specified `--content-base dist` in `start:dev` script command. As we are already aware the path specified for content-base tells the webpack-dev-server where to serve the content from. It already serves the content from `project_root/dist` folder. And now we want the server to serve our CSS files from `project_root/src/css` folder. So we have a need to serve content from multiple folders of our application. Now if we check
the `devServer.contentBase` [documentation](https://webpack.js.org/configuration/dev-server/#devserver-contentbase) it says

> It is also possible to serve from multiple directories:

so we should leverage this feature. To do this we update the `scripts` section in our `package.json` in following manner

```
  "scripts": {
    "build": "webpack",
    "start:dev": "webpack-dev-server --content-base dist --content-base src/css"
  },
```

**_Note: Refer https://stackoverflow.com/a/54492872/936494 regarding from CLI how multiple directories can be specified for `--content-base` argument)_**

Now stop and restart the server. This time when you start the server you should see logs like following:

```
> hello-world-imba@1.1.0 start:dev $project_root$
> webpack-dev-server --content-base dist --content-base src/css

ℹ ｢wds｣: Project is running at http://localhost:8080/
ℹ ｢wds｣: webpack output is served from /
ℹ ｢wds｣: Content not from webpack is served from $project_root$/dist, $project_root$/src/css
ℹ ｢wdm｣: Hash: b7dea8b91a7a1758297f
```

which informs that server is serving content from following folders: `project_root/dist`, `project_root/src/css`.

Now let's again open the URL `http://localhost:8080` and see that we get the styled page.

**Nope! It still shows unstyled page!**

OK that implies the CSS file is still not getting loaded. In that case let's try to access the CSS file directly using URL `http://localhost:8080/src/css/w3.css`.
No it shows the same error previously seen:

> Cannot GET /src/css/w3.css

OK so what could be the issue now? Pondering over how the `index.html` from `project_root/dist` is served, we don't access the `index.html` by specifying the full path
in URL i.e we don't use the URL `http://localhost:8080/dist/index.html`. Accessing that URL should show following error:

> Cannot GET /dist/index.html

But if we use the URL `http://localhost:8080/index.html` we can see the page contents being rendered. So we can infer that to access the files served from specified
content-base paths we should access those files without prefixing those file-names with those specified paths i.e. to access `w3.css` from `src/css`, we should use
the URL `http://localhost:8080/w3.css` and NOT `http://localhost:8080/src/css/w3.css`. So let's try accessing the former URL `http://localhost:8080/w3.css`. Bingo! The CSS file got loaded. So according to this finding, for the CSS to be resolved by `index.html` we should update the following line in it

```
<link rel="stylesheet" href="src/css/w3.css">
```

WITH

```
<link rel="stylesheet" href="w3.css">
```

Now try refreshing the page in browser. Bingo! Now we can see a styled Hello, World! page. Congrats!

Now save the changes made in codebase and commit the changes.
