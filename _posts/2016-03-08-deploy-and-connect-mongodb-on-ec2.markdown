---
published: true
title: Deploy and connect Mongodb on EC2
layout: post
tags: [ec2, mongodb]
categories: [server]
---
## Setup MongoDB
Some old technology, just want to sum it up all in one place.
1. create an EC2 instance on AWS, choose any Linux, I use Ubuntu
2. use apt-get to install Mongodb
Your Mongodb is ready to use, type mongo to login with default admin user.

Almost no one is using Mongodb on EC2 this way, you either want to access it with your application which also on EC2, or remote connect to your Mongodb with your development machine.

## Connect to Mongodb with Application on EC2
Connect with application on EC2, Amazon use Security group, like firewall, to control access.
Your EC2 instance with Mongodb, let's call it ec2mongo, will have a security group, and let's call the EC2 with application running ec2app, which also has its own security group. Note down the id of ec2app security group, for example sg-3987dji23. Click on ec2mongo security group->inbound->Edit, add a Custom TCP Rule, with port 27017 and fill in the source field with ec2app SG id which you note down earlier.

## Connect to Mongodb from remote machine
1. Add inbound rule to ec2mongo, choose My IP, it will resolve your public address for you.
2. SSH to your EC2, open /etc/mongod.conf with text editor. you may need sudo to write this file. change bindIp from 127.0.0.1 to 0.0.0.0 which accept connection from any.
3. Create a new database, add a user for remote login
{% highlight %}
    use newdb
    db.createUser(
    {
        user:"username",
        pwd:"password",
        roles:[{role:"readWrite",db:"newdb"}]
    })
{% endhighlight %}
you should be able to connect to ec2mongo with this shell command
`mongo hostname:27017/newdb -u username -p password`