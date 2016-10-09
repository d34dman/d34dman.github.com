---
layout : post
category : drupal
tagline: "Setting up Drupal project and fabric"
tags : [fabalicious, fabric, drupal]
permalink : blogs/drupal/fabalicious-setup.html
---

Before we start discussing about the actual setup, hope you are acquainted with the following.

- [Drupal](https://www.drupal.org) - A free and open source content managment system written in PHP
- [Git](https://git-scm.com/) - A free and open source distributed version control system
- [Fabric](http://www.fabfile.org/index.html) - A Python (2.5-2.7) library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks
- [Fabalicious](link) - A specially crafted deployment script based on fabric to help deploy drupal installations across different servers


## Adding fabalicious to your project

#### STEP 1: Install **fabric**

_Skip this step if fabric is already installed in your system._

You can install fabric using **pip** by running following command.

```
pip install fabric
```

For more detailed explanation on how to install fabric, visit this [link](http://www.fabfile.org/installing.html).

#### STEP 2: Add **fabfile.py** to project

Copy fabfile.py from the fabalicious project directly to your project.

**OR**

Optionally (recomended) add fabalicious as submodule if you are already using git to manage your project. To add fabalicious as submodule, do the following,

- Create a folder named **_tools** in your project root.
- Add **fabalicious** as a **git submodule** under this folder.
- Create a symbolic link to **_tools/fabalicious/fabfile.py** in the project root.


```
mkdir _tools
git submodule add https://github.com/factorial-io/fabalicious.git _tools/fabalicious
ln -s _tools/fabalicious/fabfile.py fabfile.py
git add fabfile.py
git commit -m "Add fabalicious as submodule"
```
#### STEP 3: Create project specific **fabfile.yaml**
Create fabfile.yaml under project folder and populate it with server details.

```yaml
name: My awesome project
requires: 2.0
needs:
  - ssh
  - git

# Our list of host-configurations
hosts:
  dev:
  type: dev
  host: example.com
  user: root
  port: 22
  rootFolder: /var/www
  filesFolder: /sites/default/files
  siteFolder: /sites/default
  backupFolder: /var/backups
```
#### Using Fabalicious

Fabalicious command structure if of format `fab config:<host> <task>`.

#### Example

![Fabalicious Command](/assets/images/fabalicious-command-breakdown.png "Fabalicious command structure.")

If [drush](http://www.drush.org/en/master/) is installed on your dev server then you can try clearing cache by running `fab config:dev drush:'cc all'`

```
$ fab config:dev drush:'cc all'
Fabalicious deployment scripts

[104.131.xxx.xxx] Executing task 'drush'
[notice] 'all' cache was cleared.

Done.
Disconnecting from 104.131.xxx.xxx... done.
```
_TIP: If you include your public key to authorized keys on the remote server then it wont prompt for your password_

To see a full list of supported task, please checkout [the tasks documentation in fabalicious](https://github.com/factorial-io/fabalicious#tasks) project.



## What next?

##### Using [Gitflow](https://github.com/nvie/gitflow) with fabalicious

Gitflow is [a successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/) by Vincent Driessen.

- A simple and elegant git branching workflow easy to understand and follow.
- Has a git extention which provides high level repository actions to make it developer life easier.
- Has become popular and comes integrated with some git client ui.

In `fabfile.yaml`, you can specifiy the branch that needs to be checked out and pulled while deploying. Thus you continue with your gitflow workflow where development will checkout develop branch, production will checkout master branch and so on.

```yaml
hosts:
dev:
branch: develop
type: dev
host: example.com
```

