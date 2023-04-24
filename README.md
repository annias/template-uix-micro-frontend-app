# Install dependencies
https://dev.to/giveitatry/microfrontends-in-reactjs-do5
```
yarn install
``` 

# Build widget

```
yarn build:widget
```



Microfrontends in ReactJS
#
webdev
#
react
#
javascript
Microfrontends are an architectural approach to building web applications by breaking them down into smaller, independent, and self-contained units that can be developed, tested, and deployed independently of each other. Each microfrontend is responsible for rendering a small part of the user interface, and they communicate with each other through well-defined APIs.

The benefits of microfrontends include:

Independent development: Developers can work on different parts of the application without worrying about breaking other parts.

Scalability: Microfrontends can be scaled independently based on the load on each module, allowing for better resource utilization.

Reusability: Microfrontends can be reused across different applications, reducing development time and costs.

Better maintainability: Microfrontends can be easily replaced or updated without affecting other parts of the application, making it easier to maintain and upgrade the application over time.

Technology agnostic: Each microfrontend can be developed using different technologies, allowing developers to choose the best tool for the job and avoiding technology lock-in.

There is a project that utilizes microfrontends particularly effectively. You can leverage their microfrontend implementation by either duplicating their code onto your page, or integrating their script to dynamically inject microfrontends into any page.

Tracardi uses micro application to interact with customers. Micro-apps are regular ReactJs, or Angular, or plain Javascript apps that can be bundled into single javascript file and injected into the webpage.

We can use a ReactJs template repo for new ReactJs app.

To start developing Micro Frontend App use the tracardi-uix-template on http://github.com/tracardi/tracardi-uix-template

Goto http://github.com/tracardi/tracardi-uix-template
Click button Use this template. Big green button at the top right side of the screen.
Enter the name of your repository
This will create a repo with files needed to start working on the app. Now its time to clone the repo and enter its folder and type

npm install

This will install all the required dependencies.

Source code
Now, let's inspect the repo folders and find out what we have here:

There are two main folders:

public - it holds the index.html file that you can use later for testing your micro-frontend-app
src - this is where is your code. We will inspect this folder further below
The other files are package.js and README.md with installation tips.

Tip: Please edit the app name in package.js. {"name": "<name-widget>", ...}

Source folder
Inside src folder you will find a index.js file. This is where your app starts. Please change the
const widgetName = 'tracardi-uix-your-name'
Copy and Save
Share
to have the name of your app.

Then there is App.js. This is the injected micro-frontend-app. Notice that it takes the domElement.
import React from 'react';
import "regenerator-runtime/runtime";

function App({domElement}) {  // (1)

    const attribute = domElement.getAttribute("data-attribute1") || "I am test attribute"
    return <h1>"Hello world " + {attribute}</h1>

}

export default App;
Copy and Save
Share
Dom parameter used to read the configuration of the micro-frontend-app. The configuration will be filled in Tracardi and passed as data attributes.
This is because we need some way to pass the parameters to the app. And it is done by defining them inside the dom elements like this.

<div class="tracardi-uix-your-name" data-attribute1="my-attribute1" data-attribute2="my-attribute1"></div>
<script src="widget/index.js"></script>
Copy and Save
Share
Tip: Please notice that the class of the div must be the same as the application name defined in the index.js file. See below.

Connecting the app container with the code
You will need to define connection between the HTML and your app. The src/index.js file is responsible for this. It is done in const widgetName = 'tracardi-uix-your-name' and const widgetDivs = document.querySelectorAll('.'+widgetName) line. You also need to reference this name in index.html. See the class of a div.

File: src/index.js
    import React from 'react';
    import ReactDOM from 'react-dom';
    import App from './App';

    const widgetName = 'tracardi-uix-your-name'  // (1)
    const widgetDivs = document.querySelectorAll('.'+widgetName)

    widgetDivs.forEach(Div => {
        ReactDOM.render(
            <React.StrictMode>
                <App domElement={Div} />
            </React.StrictMode>,
            Div
        );
    })
Copy and Save
Share
Your app placeholder class name. Please change it to yout app name and prefix it with: tracardi-uix-. This name must also be a class name for div tag in index.html
File: public/index.html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <!-- (1) -->
        <div class="tracardi-uix-your-name" data-attribute="my-attribute"></div> 
        <script src="../widget/index.js"></script>
    </body>
    </html>
Copy and Save
Share
Class name of div must be the same as widgetName in src/index.js
Building micro frontend app
When you are done, use yarn build:widget to generate the micro frontend JS file. The build operation should create index.js file in a widget folder.

Micro frontend app design
Most apps triggered on the customers pages are popup apps so make sure you use some popup canvas, or a drawer where you place your app.

Info: We use material-ui for this but this is up to you how you handle it.

Here is a simple example of an App where we use Snackbar from material-ui to display content.
import React, {useState} from 'react';
import Snackbar from '@mui/material/Snackbar';
import Box from "@mui/material/Box";
import {Button, Typography} from "@mui/material";

function App({domElement}) {

    const title = domElement.getAttribute("data-title") || null
    const message = domElement.getAttribute("data-message") || null
    const ctaLabel = domElement.getAttribute("data-cta-button") || null
    const ctaLink = domElement.getAttribute("data-cta-link") || null
    const cancelLabel = domElement.getAttribute("data-cancel-button") || null
    const vertical = domElement.getAttribute("data-vertical") || "bottom"
    const horizontal = domElement.getAttribute("data-horizontal") || "right"
    const autoHide = domElement.getAttribute("data-auto-hide") || "60000"
    const borderRadius = domElement.getAttribute("data-border-radius") || "2"
    const borderShadow = domElement.getAttribute("data-border-shadow") || "1"
    const minWidth = domElement.getAttribute("data-min-width") || "300"
    const maxWidth = domElement.getAttribute("data-max-width") || "500"

    const [open, setOpen] = useState(true)

    const handleClose = (event, reason) => {
        if (reason === 'clickaway') {
            return;
        }

        setOpen(false);
    };

    return (
        <Snackbar open={open} autoHideDuration={autoHide}
                  onClose={handleClose}
                  anchorOrigin={{vertical, horizontal}}
        >
            <Box
                sx={{
                    bgcolor: 'background.paper',
                    boxShadow: parseInt(borderShadow),
                    borderRadius: parseInt(borderRadius),
                    p: 2,
                    minWidth: parseInt(minWidth),
                    maxWidth: parseInt(maxWidth)
                }}
            >
                <Box sx={{padding: 1}}>
                    {title && <Typography variant="h5" style={{color: "black"}}>{title}</Typography>}
                    {message && <Typography style={{color: "black"}}>{message}</Typography>}
                </Box>

                {ctaLabel && <Box sx={{
                    display: "flex",
                    justifyContent: "center",
                    padding: 1
                }}>
                    {ctaLabel && <Button variant={"contained"} href={ctaLink} style={{margin: "0 5px"}}>
                        {ctaLabel}
                    </Button>}
                    {cancelLabel && <Button variant={"outlined"} onClick={handleClose}>
                        {cancelLabel}
                    </Button>}
                </Box>}

            </Box>
        </Snackbar>
    );
}

export default App;
Copy and Save
Share
You can also use other overlays from material design like modal,etc. If material-ui is not your thing you may use any other library. It is your choice.

Testing
You can also test the react app as regular app with yarn start. When the app is finished, you will have to bundle it and open on the index.html in your browser and see if it loads.

Testing bundled app file
First you will have to build the app with yarn build:widget. It will create a index.js file in widget folder.

To test if your app works as micro-app paste the bundled file widget/index.js into a regular HTML page.

Tip: You do not have to create index.html by your self. We have one created for you in the repo in the public directory. It is called index.html. Paste the script tag together with the configuration inside the body of the index.html and open the file.
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>

    <!-- (1) -->
    <div class="tracardi-uix-cta-message"
         data-title="title"
         data-message="message"
         data-cta="visit" data-cancel=""
         data-auto-hide="50000"
    ></div>

    <!-- (2) -->
    <script src="../widget/index.js"></script>
  </body>
</html>
Copy and Save
Share
The app container with the configuration in data attributes. Please notice that the class of the div must be the same as the application name defined in the index.js file.
The bundled app script
Now open the index.html in the browser, and it should load your app and display it on top of the existing index.html. If the index.html does not have any html then it will be empty as in our example.

How Tracardi injects the app
Tracardi loads the app the same as we did it in the test example above. That means with a script tag and a div container. It appends the needed tags at the bottom of the page. Like this.

<div class="tracardi-uix-your-name" data-attribute="my-attribute"></div>
<script src="widget/index.js"></script>
Copy and Save
Share
Passing data from Tracardi to micro app
The micro app may need configuration. For example the pop-up message app needs message and maybe location of the pop-up window. To configure the app and pass data from Tracardi use data attributes. It can be done in src/App.js

File: src/App.js
    import React from 'react';
    import "regenerator-runtime/runtime";  // (3)

    function App({domElement}) {

        const attribute1 = domElement.getAttribute("data-attribute-1") || "I am test attribute 1"  // (1)
        const attribute2 = domElement.getAttribute("data-attribute-2") || "I am test attribute 2"  // (2)
        return <h1>"Hello world " + {attribute}</h1>

    }

    export default App;

Copy and Save
Share
This is your app input data. Read it like this, and then you can use attribute1 variable in you app. See index.html on how to pass data.
This is your app input data. Read it like this, and then you can use attribute2 variable in you app.
This import may be needed when you use async functions. See Trouble-shooting below.
File: index.html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <div class="tracardi-uix-your-name" 
             data-attribute-1="my-attribute-1"
             data-attribute-2="my-attribute-2"></div>
        <script src="widget/index.js"></script>
    </body>
    </html>
Copy and Save
Share
Troubleshooting
If you use async functions in your app (what is very probable) you may encounter the problem that the app does not build when build with yarn build:widget. Then follow the tip below.

Error "regeneratorRuntime is not defined error"
When you see "regeneratorRuntime is not defined" that means you have to add import "regenerator-runtime/runtime"; to your component.
