---
layout : post
category : drupal
tagline: "Drupal meets Fabric"
tags : [intro, beginner, fabalicious, fabric, drupal, automation]
permalink : blogs/drupal/fabalicious-intro.html
---
Managing multiple instances of Drupal using fabalicious powered by fabric.

This is a typical deployment cycle that we follow,

- SSH : into development server
- TERMINAL : navigate to project root folder
- DRUSH : put site into maintenance mode
- DRUSH : take database backup
- GIT: pull develop branch from upstream
- DRUSH : run database updates
- DRUSH : clear cache

Later, run similar steps for production too, with few changes. As the
number of projects grew, so did the number of instances of Drupal we had to
deploy and maintain.

### The Problem
* Needed some way to keep track of all the instances related to a particular project.
* Wanted somebody to take care of the mundane repititive task of deployment.

We definitely needed some script to automate the whole process. Thats where
fabric comes for help.

### What is fabric?
"Fabric is a Python (2.5-2.7) library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks." ~ http://www.fabfile.org/

So after installing fabric, you can create two files inside your project folder.

    fabfile.py : this file is where you define your tasks
    fabfile.yaml : this file would hold the project and server specific configurations

So let say we wrote  task called "deploy" in fabfile.yaml and specified our configuration
for development server in fabfile.yaml under key "develop".
Then we could simply do `fab config:develop deploy`. While the fabric runs all the
deployment task for you, you can go and grab a coffee, or collect your dark elixers from Clash of Clans, or tell me what you did with the extra time you got in comments below.

Over time you keep on adding more and more tasks into fabfile.py and eventually
start adding it as a submodule to every project. Thats how fabalicious was born.

fabalicious is specially crafted deployment script to help deploy drupal installations across different servers. It reads a yaml-file called "fabfile.yaml" where all hosts are stored.

project link: https://github.com/factorial-io/fabalicious

Basic Usage Example.

Prerequisite:
* You have ssh access to the servers where you want to peform tasks.
* The individual components of tasks could be executed on the server.

@TODO
