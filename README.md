# op-static-site-template

This repository an example template static site for open projects. It makes use of Hugo, a customizeable Hugo theme, github-driven continuous deployment, and hosting with Netlify:

* https://gohugo.io/about/
* https://themes.gohugo.io/gohugo-theme-ananke/
* https://gohugo.io/hosting-and-deployment/hosting-on-netlify/

You can see a live demo at https://pedantic-sinoussi-974ee3.netlify.com/

## how to customize this

### template & theme - different purposes, repos

The template is where all your content will live. Posts, pages, configs, images, project-specific layouts, etc. 

1. Update the strings in the config.toml file
1. Add your own images and logo to the /images directory
1. Update the content on the main and about pages

The theme is where the default styling, templates & partials, and other page logic lives. OASIS has a fork of the Ananke theme in order to provide some customizations by default, but projects can easily install other Hugo themes or make their own. 

## deploying changes

If you are updating your template - meaning that you are adding or editing content, images, or configs - all you have to do to deploy those changes (once you've tested them locally, of course) is push to your master branch. Netlify automatically rebuilds and deploys the site for you.

If you want to update the theme - which is any file that is loaded into the theme directory - note that those files are loaded in as a submodule, which means its a different repo. Projects can:

1. file an issue or PR against the OASIS theme repo to incorporate changes that may improve the theme for all projects
1. add their own layouts, partials & styling into the appropriate directories on their template repo
1. fork the OASIS theme repo; make whatever changes they'd like to make and the fork in as the theme submodule
1. choose an altogether different theme at https://themes.gohugo.io

Again, you will simply to push to master on the appropriate theme repo. You will also need to trigger Netlify to rebuild the site, which you can do from your Netlify dashboard.

