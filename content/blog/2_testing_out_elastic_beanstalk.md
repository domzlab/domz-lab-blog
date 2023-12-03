---
title: Deploying a NodeJs application on Elastic Beanstalk
description: 
permalink: "/blog/testing_out_elastic_beanstalk/"
date: Last Modified
templateEngineOverride: md
tags:
  - Elastic Beanlstak
  - NodeJs
---

### Introduction

I've recently started preparing to write AWS Certified DevOps Engineer Professional Exam. I'm currently studying through a course of ACloudGuru that helps you prepare to sit for the exam.

One of the topics covered in this course is Elastic Beanstalk. In my work as Cloud Support Engineer, I have yet come encounter a situation where I need to troubleshoot issues with this service. Thus, to get a general idea of how this service works, I thought it would be a good idea to see how one would go about deploying a NodeJs application on Elastic Beanstalk.

### Development Environment


```sh
mkdir simple-app
cd simple app
touch index.js
```

Theafter, run `eb init -`

Select a default region
selected 4) eu-west-1 : EU (Ireland)

Note, you would type the number you want to select, then hit enter to move to the next option. 

Select an application to use
selected 3) [ Create new Application ]

Enter Application Name


Select a platform branch.
selected 6) Node.js

selected option 1) Node.js 18 running on 64bit Amazon Linux 2023


### References:
[1] Deploying an Express application to Elastic Beanstalk - https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_nodejs_express.html
[2] https://www.digitalocean.com/community/tutorials/how-to-create-a-web-server-in-node-js-with-the-http-module