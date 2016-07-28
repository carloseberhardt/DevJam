#Lab 2 -  Using apigeetool

##Overview

We have used the management APIs to interact with Edge. There are many
tools which we have built which leverages the APIs to do specific tasks.

One of the most commonly and heavily used tools is apigeetool. This is a
node.js based command line tool which enabled you to deploy API proxies
and Node.js applications to the Apigee Edge platform. The tool also lets
you list and undeploy API proxies.

This is especially handy if you are trying to deploy node.js apps in
apigee.

**Estimated time:**30 mins

**Lab Steps**

**Phase 1:**I**nstallation**

**Step 1.**Install node

If you are on Mac Download and install node from here:
[*https://nodejs.org/en/*](https://nodejs.org/en/)

If you are on windows then download and install node from here:
[*https://nodejs.org/en/download/*](https://nodejs.org/en/download/)

If you are on linux please download and install from here:
[*https://github.com/nodesource/distributions*](https://github.com/nodesource/distributions)

**Step 2.**Install apigeetool

Command:
```
npm install -g apigeetool
```

**NOTE**: The -g option places the apigeetool command in your PATH. On
"\*nix"-based machines, sudo may be required with the -g option. If you
do not use -g, then you need to add the apigeetool command to your PATH
manually. Typically, the -g option places modules in:
/usr/local/lib/node\_modules/apigee-127 on \*nix-based machines.

Or you can install it by cloning it from github:
```
$ git clone https://github.com/apigee/apigeetool-node.git

Usage: apigeetool <command>

Valid commands:

deployproxy Deploy API Proxy
deploynodeapp Deploy Node.js Application
listdeployments List Deployments
undeploy Undeploy Proxy or Node.js Application
fetchproxy Download Proxy bundle
getlogs Get Application logs
delete Delete undeployed Proxy or Node.js Application
```

Lets see if it has been successfully installed. Go to command like on
your laptop and type:

```
$ apigeetool -h
```

You should see a screen with the available commands.

Now we are going to use the newly installed apigeetool to
deploy/undeploy proxies..

**TIP:** Get help on specific command: apigeetool deploynodeapp -h

**Phase 2:** Using apigeetool

Now that we have installed apigeetool, we are going to use it to
interact with the org.

**Step 1:** Get list of all the deployed proxies:

apigeetool listdeployments -u {username} -o {org-name} -e test

The above script will do the following:

It will GET the list of all the proxies in a json format from the “test”
environment.

o = specifies your org
e = specifies the environment
u = username

You will be prompted for password.

Your response will be like this:

"oauth\_devjam2" Revision 1
deployed
environment = test
base path = /
"weatherapi" Revision 1 deployed
environment = test
base path = /

Here you can see the details of each deployed proxy.

**Step 2.** Deploy a node.js app

Create a new script called opsjam.js

Put the following script in it:

```
var express = require('express');
var app = express();

app.set('port', process.env.PORT || 3000); // When running local, app
//will listen on port 3000

app.get('/hello', renderHello);

function renderHello(req, res) {
  // res.writeHead(200, {'Content-Type': 'text/html'});
  res.end("Hello World");

}

var port = 8000;
app.listen(port);

console.log('Server running at http://127.0.0.1:%d/', port);
```

**Note:** Make sure you are in the same folder where your node script is

```
apigeetool deploynodeapp -u {username} -o {opsjam\_org\_name} -e test -n
"YourName node app" -d . -m opsjam.js -p Welcome\_123 -b opsjam

"node app" Revision 1
deployed
environment = test
base path = /
URI = *http://{org-name}-test.apigee.net/*
```

Once you run the above command apigeetool will bundle the node.js script
as a node app and will deployed it in the selected org.

Use -V to debug if you need to see what the command is doing.

![](.//media/image03.png)

Go to the UI and go to APIs-&gt;API Proxies and you should be able to
see your deployed proxy

Once you open the proxy it should look like this:

![](.//media/image05.png)

You can see the code which you just deployed in the Code window. If you
want you can edit the code in the same window or even add more files to
it.

You should be able to invoke the deployed app from any browser or any
Rest Client from an URL like this:
[*http://pixvy-test.apigee.net/opsjam/hello*](http://pixvy-test.apigee.net/sarthak/hello)

You should be able to see a Hello World response from your browser which
looks like this:

![](.//media/image01.png)

You can try out additional operations like retrieving node.js logs,
deploying/undeploying proxies etc. using the tool.

You can learn more about apigeetoold here:
[*https://github.com/apigee/apigeetool-node*](https://github.com/apigee/apigeetool-node)
