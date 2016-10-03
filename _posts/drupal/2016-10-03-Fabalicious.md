---
layout : post
category : drupal
tagline: "Drupal meets Fabric"
tags : [intro, beginner, fabalicious, fabric, drupal, automation]
permalink : blogs/drupal/fabalicious-intro.html
---
# Managing multiple instances of Drupal using fabalicious powered by fabric

This is a typical deployment cycle that we follow,

1. `SSH` : into development server
2. `TERMINAL` : navigate to project root folder
3. `DRUSH` : put site into maintenance mode
4. `DRUSH` : take database backup
5. `GIT` : pull develop branch from upstream
6. `DRUSH` : run database updates
7. `DRUSH` : clear cache

Later, run similar steps for production too, with few changes. As the number of projects grew, so did the number of instances of Drupal we had to deploy and maintain.

### The Problem
* Needed some way to keep track of all the instances related to a particular project.
* Wanted somebody to take care of the mundane repititive task of deployment.

We definitely needed some script to automate the whole process. That is where fabric comes for help.

### What is fabric?
>Fabric is a Python (2.5-2.7) library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks. 
~ http://www.fabfile.org/

So after installing fabric, you can create two files inside your project folder.

    fabfile.py
    fabfile.yaml

`fabfile.py` is where you would define your tasks. Additionally for convinience we would load project specific configurations from `fabfile.yaml`. This is so that we could reuse `fabfile.py` in other projects without hacking it everytime.

So let say we wrote  task called **deploy** in `fabfile.py` and specified our configuration for development server in `fabfile.yaml` under key **develop**. Then we could simply do `fab config:develop deploy`. While the fabric runs all the deployment task for you. You can go, grab a coffee, or collect your dark elixers from Clash of Clans, or tell me what you did with the extra time you got in comments below.

Over time you keep on adding more and more tasks into fabfile.py and eventually start adding it as a submodule to every project. Thats how [fabalicious](https://github.com/factorial-io/fabalicious) was born.

fabalicious is specially crafted deployment script to help deploy drupal installations across different servers. It reads a yaml-file called `fabfile.yaml` where all project specific hosts are defined.

Basic Usage Example.

Prerequisite:
* You have ssh access to the servers where you want to peform tasks.
* The individual components of tasks could be executed on the server.

@TODO
