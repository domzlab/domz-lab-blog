---
title: Deploying a NodeJs application on Elastic Beanstalk
description: 
permalink: "/blog/testing_out_elastic_beanstalk/"
date: 2023-12-04
templateEngineOverride: md
tags:
  - Elastic Beanlstak
  - NodeJs
---

## Introduction

I've recently started preparing for the AWS Certified DevOps Engineer Professional Exam. I'm currently studying through a course on ACloudGuru that helps you prepare to sit for the exam.

One of the topics covered in this course is Elastic Beanstalk. In my work as Cloud Support Engineer, I have yet come encounter a situation where I need to troubleshoot issues with this service. Thus, to get a general idea of how this service works, I thought it would be a good idea to see how one would go about deploying a NodeJs application on Elastic Beanstalk.

## Setting up my Development Environment

To create and deploy updates to an Elastic Beanstalk environment, I was using Elastic Beanstalk Command Line Interface (EB CLI) is a command line client. I used the setup scripts seen in [1] to install the EB CLI to my local machine.


To get started, I created a new directory for your project files. In the newly created directory, I then added an index.js file. Below are the commands I used to do this from command line.

```sh
mkdir simple-app
cd simple app
```

Thereafter, I used the EB CLI command `eb init -i` to set the default values for my elastic beanstalk environment. Below are the options that I selected:

```sh
$ eb init -i

Select a default region
1) us-east-1 : US East (N. Virginia)
2) us-west-1 : US West (N. California)
3) us-west-2 : US West (Oregon)
4) eu-west-1 : EU (Ireland)
...
(default is 3): 4


Select an application to use
1) simple-app
2) first-servers
3) nodejs-example-express-rds
4) [ Create new Application ]
(default is 1): 4


Enter Application Name
(default is "simple-app2"): 
Application simple-app2 has been created.
Select a platform.
1) .NET Core on Linux
2) .NET on Windows Server
3) Docker
4) Go
5) Java
6) Node.js
7) PHP
8) Packer
9) Python
10) Ruby
11) Tomcat
(make a selection): 6

Select a platform branch.
1) Node.js 18 running on 64bit Amazon Linux 2023
2) Node.js 18 running on 64bit Amazon Linux 2
3) Node.js 16 running on 64bit Amazon Linux 2 (Deprecated)
4) Node.js 14 running on 64bit Amazon Linux 2 (Deprecated)
(default is 1): 1

Cannot setup CodeCommit because there is no Source Control setup, continuing with initialization
Do you want to set up SSH for your instances?
(Y/n): n
```

Note, you would type the number you want to select, then hit enter to move to the next option. 

## Application Code

Still in the simple-app directory, I created a file named index.js. I used code below:

```js
const http = require("http");
const host = 'localhost';
const port = 8080;

const requestListener = function (req, res) {
    res.writeHead(200);
    res.end("Hello World!");
};

const server = http.createServer(requestListener);
server.listen(port, host, () => {
    console.log(`Server is running on http://${host}:${port}`);
});

```

To test this simple server, I ran `node index.js`. Then, in another terminal window, ran curl localhost:8080. The output returned was as follows:


```sh
$ curl localhost:8080

Hello World!
```

## Deploy first build of application

To deploy this application to elastic beanstalk, run `eb create`. I used the default values for each option in the command prompt:

```sh
$ eb create

Enter Environment Name
(default is simple-app2-dev): 
Enter DNS CNAME prefix
(default is simple-app2-dev): 

Select a load balancer type
1) classic
2) application
3) network
(default is 2): 2


Would you like to enable Spot Fleet requests for this environment? (y/N): N
```

After running `eb create`, the deploy status was logged in the terminal (provided that you didn't run Ctrl+C).

The first run did create the environment, but resulted in the following error:

```sh
2023-12-04 11:59:07    ERROR   Instance deployment failed to generate a 'Procfile' for Node.js. Provide one of these files: 'package.json', 'server.js', or 'app.js'. The deployment failed.
2023-12-04 11:59:09    ERROR   [Instance: i-abcef1234] Command failed on instance. Return code: 1 Output: Engine execution has encountered an error..
2023-12-04 11:59:09    INFO    Command execution completed on all instances. Summary: [Successful: 0, Failed: 1].
2023-12-04 12:00:12    ERROR   Create environment operation is complete, but with errors. For more information, see troubleshooting documentation.
                                
ERROR: ServiceError - Create environment operation is complete, but with errors. For more information, see troubleshooting documentation.
```

From the output, we can see that the deployment failed due to Elastic Beanstalk not being able to create a Procfile. From this, I learned that you would either need to create a package.json file for your project, or name started file server.js or app.js.

In my case, what I did is create a Procfile in the route of my directory. And in that file, I specified index.js 

```text
### Contents of Procfile

web: node index.js
```

## Deploy new revision of application

I then ran `eb deploy` to deploy a new revision of my application.

After adding the Procfile to my project, the deployment completed successfully.


```sh
$ eb deploy

Creating application version archive "app-***".
Uploading simple-app2/app-***.zip to S3. This may take a while.
Upload Complete.
2023-12-04 12:14:02    INFO    Environment update is starting.      
2023-12-04 12:14:05    INFO    Deploying new version to instance(s).
2023-12-04 12:14:14    INFO    Instance deployment completed successfully.
2023-12-04 12:14:19    INFO    New application version was deployed to running EC2 instances.
2023-12-04 12:14:19    INFO    Environment update completed successfully.
```

To view an EB application, you can run `eb open`. This should open the application URL in a browser. Alternatively, run `eb status`. In the command output, you would see the CNAME, is the unique URL to access to your Elastic Beanstalk application.

## Final update to application

To end off, I made one more change to my index.js file. As seen below, I updated my source code to return the request headers as an HTML response.

```js
const http = require("http");
const host = 'localhost';
const port = 8080;

/**
 * Returns the request headers as a response
 */
const requestListener = function (req, res) {

  let arr = [];
  for (const [key, value] of Object.entries(req.headers)) {
        arr.push(`<p>${key}: ${value}</p>`);
  }
  
  res.setHeader("Content-Type", "text/html");
  res.writeHead(200);
  res.end(`<html><body><h2>Request Headers</h2>${arr.join('')}</body></html>`);
};

const server = http.createServer(requestListener);
server.listen(port, host, () => {
    console.log(`Server is running on http://${host}:${port}`);
});

```

As before, I ran `eb deploy` to deploy a new revision. Once the deployment completed successfully, I used the CNAME of my EB application test the request on my web browser. 

![](/img/test_out_elastic_beanstalk/1_view_response_in_browser.png)

Thank you for reading.

## References:

[1] Install the EB CLI - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html

[2] Deploying an Express application to Elastic Beanstalk - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_nodejs_express.html

[3] https://www.digitalocean.com/community/tutorials/how-to-create-a-web-server-in-node-js-with-the-http-module