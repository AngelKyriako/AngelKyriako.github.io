---
title: A static blog site with Hexo
---

## Intro
After reading [this](https://risingstars2016.js.org/?ref=freecodecamp-loves-you#ssg) article a while ago, I thought of using [Hexo](https://hexo.io/) to build a personal web site. Its only natural the first post in here to be about it.

Hexo is an awesome tool to create a simple static website. It has a very easy to use command line interface & comes with plugins that can enable automatic deployment or hot reloading during development. Lets see it through.

**The following steps were tested from an Ubuntu 16.04.1 LTS machine.**

### Prerequisites
- A bash terminal
- the [node.js](https://nodejs.org/en) runtime
- [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

### Setup nvm (optional)
[nvm](https://github.com/creationix/nvm) will let us change the node.js version with ease, if required in the future, and preserve our sanity while doing so.

```sh
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
```
Make sure to checkout the [nvm documentation](https://github.com/creationix/nvm) for an up to date installation guide.

Verify the installation by hitting an nvm command.
```sh
$ nvm --version
$ nvm ls
```

### Setup node.js & npm
Install the long time support(LTS) node.js version using nvm.

```sh
$ nvm install lts/*
$ nvm ls
```

Then set the node.js version to the LTS just downloaded, in case it does not set it automatically.
```sh
$ nvm use lts/*
$ nvm ls
```

Verify the installation.
```sh
$ node --version
$ npm --version
```

If you do not want to use nvm download node.js from the official [website](https://nodejs.org/en/download).

### Setup git
Install with the apt-get tool.
```sh
$ sudo apt-get install git-all
```

Verify the installation.
```sh
$ git --version
```

## Install Hexo
Use the node package manager(npm) to install the hexo command line interface(CLI).
```sh
$ npm install -g hexo
```
This will enable us to use hexo commands for our development process.
**ALL hexo commands must be run from the root of project.**

## Initialize the project
```sh
$ hexo init sitename
$ cd sitename
```

From now on we assume that we are inside the project's directory at *'$/sitename/'* path, all paths & commands listed below are relevant to this path.

Verify that the site can be served properly.
```sh
$ hexo server
```
Hit *'localhost:4000'* in your browser, you should see the default hexo blog.

## Pick a theme (optional)
Look for the theme you prefer from [this list](https://hexo.io/themes/) and follow its documentation. In our case we are gonna use [cactus-dark](https://github.com/probberechts/cactus-dark) which is what this site is build upon.

### Add as git submodule
We will add the theme as a submodule in our project. To do this we need to initialize git for this project.
```sh
$ git init
```

Verify that git is enabled for the project
```sh
$ git status
```

Add the theme as a submodule.
```sh
$ git submodule add https://github.com/AngelKyriako/cactus-dark.git themes/cactus-dark
```

This allows easy updates, whenever the theme us updated by its author.
```sh
$ git submodule foreach git pull origin master
```

It is recommended to fork the theme repository so that you can update it, in case you need any customizations.

### Set theme
Open *'_config.yml'* file with a text editor, look for the *'theme'* key and set it to *'cactus-dark'*.
```yaml
# this is a comment in .yml files
# ...
#
# theme: landscape
theme: cactus-dark
#
# ...
```

### Theme configuration
Update the *'themes/cactus-dark/_config.yml'* file to configure the theme's settings like google analytics, disqus comments & logo.

Restart the server for hexo to load the configuration changes. You should now be able to see the site with the theme's UI.

## Development
In hexo, the content of the site is saved under the *'source'* directory.

Hexo, by default, uses *.md*(markdown) files for its content. A nice cheatsheet can be found [here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet). You may also want to checkout [dillinger.io](http://dillinger.io/), which also has a default markdown file as a starting point, where you can see most common syntax.

### Add a post
Posts are entities that grouped together sum up to a blog. In most themes they can be found in a *'Writings'* section in the home page.

The following command will create a markdown file at *'source/_posts/my-first-post.md'*.
```sh
$ hexo new post my-first-post
$ hexo server
```
Navigate to the site to checkout the new post.

### Add a draft
A draft is actually an unfinished post and the process to create one is quite similar.

The following command will create a markdown file at *'source/_drafts/my-future-post.md'*.
```sh
$ hexo new draft my-future-post
$ hexo server
```
Notice that *'my-future-post'* cannot be found in the web site.

However, a draft can be promoted to a post when ready. Therefore it is useful for work in progress that should not be visible in the site until its ready.

You can run the server with a draft option during development so that drafts will be visible like they were finished posts.
```sh
$ hexo server --draft
```

Now, lets assume that *'my-future-post'* is now ready. Promote it from a draft to a post with the command below.
```sh
$ hexo publish my-future-post
```
Now the draft file will be moved from *'source/_drafts/my-future-post.md'* to *'source/_posts/my-future-post.md'* and will be accessible by users.

### Add a page
In Hexo a page is an entity that is supposed to be served on a static path from the website. For example, the theme we picked has an *'about'* menu item which redirects us to *'localhost:4000/about'* but there is no page to display. Lets fix that.

The following command will create a markdown file at *'source/about/index.md'* that will be surved by default from *'localhost:4000/about'*.
```sh
$ hexo new page about
$ hexo server
```
Notice that, the *'about'* menu item redirects us to our brand new empty page.

### Hot reload (optional)
Until now we had to either restart the server or refresh the web page to see our changes. With hot reloading, hexo can automatically refresh the site whenever a change occurs. We can use a hexo plugin for that job.

Install [hexo-browersync](https://github.com/hexojs/hexo-browsersync) with npm.
```sh
$ npm install --save hexo-browersync
```
When the installation is done, hot reloading should be enabled.
- Restart the server
- Hit the site url in your browser
- Edit one of the previous *.md* files you created
- Verify that the site was automatically refreshed by the plugin

In the terminal where the server is running, you should see a log like this.
```sh
$ [BS] Reloading Browsers...
```

### Troubleshouting
Whenever hexo is logging weird error codes that cannot be found easily by googling, remember to checkout the [troubleshooting docs](https://hexo.io/docs/troubleshooting.html).

For example, in linux the following command will fix a hot reloading error due to max file watches.
```sh
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

## Deploy to github pages
Lets use [github pages](https://pages.github.com/) to deploy our site. In the end of this section you should be able to view the site from YourUsername.github.io.

### Create the github repo
Go ahead and create a repository based on your username in [github](https://github.com/).

The format should be **'YourUsername.github.io'.** Make sure to pay attention on case sensitivity.

For example, my username is *'AngelKyriako'* which makes my repository: *'AngelKyriako.github.io'.*

### Push source code to github
Github will use the master branch to server our site. Because of that, we need another branch to store our source code.

First lets commit our changes to branch *'src'*, where from we will develop the site.
```sh
git checkout -b src
git add --all
git commit -m "initial commit"
```

Now, lets push our local *'src'* branch to the repository on github
```sh
git remote add origin https://github.com/YourUsername/YourUsername.github.io.git
git push origin src
```
The github repository URL should be based on your username.

### Deploy to github pages
To deploy to github with ease, we will use another plugin.

Install [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git) with npm.
```sh
$ npm install --save hexo-deployer-git
```

After that, setup *'_config.yml'* for git deploy to the same git URL.
```yaml
deploy:
  type: git
  repo: https://github.com/YourUsername/YourUsername.github.io.git
  branch: master
  message: "Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}"
```
Notice how the deploy is set to be pushed to the master branch.

Houston, we are ready for liftoff.
```sh
$ hexo clean
$ hexo deploy
```

If you followed the steps correctly, you should be able to see your site *'YourUsername.github.io'* or *'yourusername.github.io'* in your browser. 

## On your own
- Configure *'_config.yml'* to customize your site
- Add a comments section
- Use tags and categories for posts
- Read the [hexo docs](https://hexo.io/docs/) for more info on its features.
- Create a custom plugin

Let me know of any problems you might encounter in the comments below. Good luck