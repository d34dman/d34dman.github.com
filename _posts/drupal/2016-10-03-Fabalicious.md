---
layout : post
category : drupal
tagline: "Drupal meets Fabric"
tags : [fabalicious, fabric, drupal]
permalink : blogs/drupal/fabalicious-intro.html
---
## Managing multiple instances of Drupal using fabalicious powered by fabric

A typical deployment cycle that we follow,

1. `SSH` : into development server
2. `TERMINAL` : navigate to project root folder
3. `DRUSH` : put site into maintenance mode
4. `DRUSH` : take database backup
5. `GIT` : pull develop branch from upstream
6. `DRUSH` : run database updates
7. `DRUSH` : clear cache

Later, run similar steps for production too, with few changes. 

As the number of projects grew, so did the number of instances of Drupal we have to deploy and maintain.

### The Problem

* Needed some way to keep track of all the instances related to a particular project.
* Wanted somebody to take care of the mundane repititive task of deployment.

A script to automate the whole process would be really helpful. That is where fabric comes for help.

### What is fabric?

>Fabric is a Python (2.5-2.7) library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks. 
>~ http://www.fabfile.org/

So after installing fabric, you can create two files inside your project folder.

```
fabfile.py
fabfile.yaml
```

`fabfile.py` is where you would define your tasks. Additionally for convinience we would load project specific configurations from `fabfile.yaml`. This is so that we could reuse `fabfile.py` in other projects without hacking it everytime.

So let say we wrote  task called **deploy** in `fabfile.py` and specified our configuration for development server in `fabfile.yaml` under key **develop**. Then we could simply do `fab config:develop deploy`. While the fabric runs all the deployment task for you. You can go, grab a coffee, or collect your dark elixers from Clash of Clans, or tell me what you did with the extra time you got in comments below.

Over time you keep on adding more and more tasks into fabfile.py and eventually start adding it as a submodule to every project. Thats how [fabalicious](https://github.com/factorial-io/fabalicious) was born.

fabalicious is specially crafted deployment script to help deploy drupal installations across different servers. It reads a yaml-file called `fabfile.yaml` where all project specific hosts are defined.

### Basic Usage Example.

Prerequisite:

* You have ssh access to the servers where you want to peform tasks.
* The individual components of tasks could be executed on the server.

Let us consider a situation where you deploy changes first in development server, then in a staging server and finally on the production. We populate our `fabfile.yaml` as follows

```yaml
name: myproject
deploymentModule: myproject_deploy

excludeFiles:
  backup:
    - "styles"
    - "tmp"
  copyFrom:
    - "tmp"
    - "styles"

hosts:
  staging:
    host: example.com
    user: root
    password: root
    port: 22
    # path to drupal's root folder
    rootFolder: /var/static/myproject/staging/public
    gitRootFolder: /var/static/myproject/staging/
    # path to the site's folder
    siteFolder: /sites/default
    filesFolder: /sites/default/files
    backupFolder: /var/static/myproject/staging/backups
    useForDevelopment: true
    # branch to pull
    branch: develop
    hasDrush: true
    supportsInstalls: true
    database:
      name: myproject
      user: root
      pass: admin
  release:
    inheritsFrom: staging
    host: example.com
    # branch to pull
    branch: release/0.1.0
    rootFolder: /var/static/myproject/010/public
    gitRootFolder: /var/static/myproject/010/
    database:
      name: myproject_010
      user: root
      pass: admin
  production:
    host: production.com
    user: root
    password: root
    port: 22
    # path to drupal's root folder
    rootFolder: /var/www/public
    gitRootFolder: /var/www/
    # path to the site's folder
    siteFolder: /sites/default
    filesFolder: /sites/default/files
    backupFolder: /var/www/backups
    useForDevelopment: false
    # branch to pull
    branch: master
    hasDrush: true
    supportsInstalls: false
    database:
      name: production
      user: root
      pass: admin
```

Then we could,
To deploy on staging server you could do `fab config:stage deploy`
To copy database from production to staging you could do `fab config:release copyDBFrom:production`
To execute a drush command on staging like clearing cache you could `fab config:staging drush:"cc all"`

Please [visit the project page of fabalicious](https://github.com/factorial-io/fabalicious) to know what all tasks have already been implemented.

### Conclusion

For those who don't want to get their hands dirty in scripting, fabalicious provides a decent set of tasks that you could perform with fabric on Drupal sites.
