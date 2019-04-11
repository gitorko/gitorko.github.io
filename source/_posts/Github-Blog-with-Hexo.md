---
title: Github Blog with Hexo
date: 2019-03-10 00:00:00
tags: hexo, github, google search, analytics
---

Create a blog in github using Hexo.

<!-- more -->

<!-- toc -->

## Setup

As a prerequisite you need to have nodejs and git installed. You can then install hexo.

[Hexo](https://hexo.io/)

```bash
npm install hexo-cli -g
```

The name of the project should be excat match to the github username. In this demo the github user is gitorkouser
```bash
hexo init gitorkouser
```

```bash
cd gitorkouser/
```

Run npm install

```bash
npm install
```

Run hexo server, this will bring up the server.

```bash
hexo server
```

Create a github repository

{% asset_img IMG-01.JPG %}

Install the hexo deploy plugin

```bash
npm install hexo-deployer-git --save
```

Modify the gitorkouser/_config.yml

```yaml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/gitorkouser/gitorkouser.github.io.git
  branch: master
  message: "Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}"
```

Deploy the blog, enter the username and credentials when prompted

```bash
hexo deploy
```


You should now be seeing the public html files in  your github repostiory. Hit the url [https://gitorkouser.github.io/](https://gitorkouser.github.io/) and your blog should be up.

## Source Code
Hexo takes care of publishing the public files to github master branch, we need to commit our source code files to a different branch so that we dont loose the original files.

```bash
git init .
git add .
git commit -m "base"
git branch -m master source
git branch
git remote add origin https://github.com/gitorkouser/gitorkouser.github.io.git
git push -u origin source
```

We renamed the master branch to source branch locally. All source files will reside on a different branch. After this you should have 2 branches on github one with public files and other with source code.

You can create new blogs and publish with following commands, Images can be placed in folder name with similar name as .md file. 

```bash
hexo new "blog name"
hexo generate
hexo deploy
```

Dont forget to commit your changes and push to github often.

## Images
You can embed images with the tag, Images associated with the .md files goto a folder with the same name as filename.

```html
{% asset_img IMG-01.JPG %}
```

## Menu Bar

Modify the gitorkouser/themes/landscape/_config.yml and menubar link. Notice that this config file is different and found under themes folder.

```json
menu:
  Home: /
  Archives: /archives
  About: https://github.com/gitorkouser
rss: /atom.xml
```

## Banner Image

To change the banner image edit gitorkouser/themes/landscape/source/css/_variables.styl

```html
logo-size = 40px
subtitle-size = 16px
banner-height = 100px
banner-url = "images/banner.jpg"
```

Change the jpg to update a different image for banner. The path of this banner image will be gitorko/themes/landscape/source/css/images/banner.jpg

## Favicon

Change the favicon, the png/jpg file needs to be placed under gitokouser/source/images folder. If the images folder doesnt exist create one. Then update the gitorkouser/themes/landscape/_config.yml

```json
favicon: /images/favicon.png
```

## Sitemap

Install the sitemap plugin

```bash
npm install hexo-generator-sitemap --save
```

Deploy the site and the sitemap should be available on [https://gitorkouser.github.io/sitemap.xml](https://gitorkouser.github.io/sitemap.xml)

## Root URL

Modify the gitorkouser/_config.yml and change the root url.

```json
url: http://gitorkouser.github.io
```

## Table of Contents

Install table of contents plugin. 

```bash
npm install hexo-toc --save
```
To enable table of contents insert the tag

```html
<!-- toc -->
```

## Collapsed page

To collapse the page enable this tag in the .md file

```html
<!-- more -->
```

Your blog page should now be collapsed and comes with a 'Read More' link.

{% asset_img IMG-02.JPG %}

## Search within blog

Install the search plugin to allow users to search within the blog. This generates a search index file, which contains all the neccessary data of your articles that you can use to write a local search engine for your blog.

```bash
npm install hexo-generator-search --save
```

## Discuss Comments

Modify the gitorkouser/_config.yml and add your disqus account name. You can create your disqus account [https://disqus.com/](https://disqus.com/)

```json
disqus_shortname: gitorko
```

## Feed

To enable feed install the feed plugin

```bash
```

Add the feed config to gitorkouser/_config.yml file.

```yaml
feed:
  type: atom
  path: atom.xml
  limit: 20
```


## Google Analytics

Modify the gitorkouser/_config.yml and add your google analytics tracking id. The default theme supports it.

```json
theme_config:
  google_analytics: UA-1234234
```
This will help you track your website traffic

{% asset_img IMG-03.JPG %}

{% asset_img IMG-04.JPG %}

## Google Search Indexing

By default google search will not hit your blog. 

{% asset_img IMG-05.JPG %}

To get your site to show up in google search generate the sitemap.xml as stated in the above section.  Create a robots.txt under source folder to inform with web crawlers on what files need to be excluded

```html
User-agent: *
Allow: /
Allow: /archives/
Allow: /categories/
Allow: /tags/
Allow: /about/

Disallow: /vendors/
Disallow: /js/
Disallow: /css/
Disallow: /fonts/
Disallow: /vendors/
Disallow: /fancybox/

Sitemap: https://gitorkouser.github.io/sitemap.xml
```

Login to google search console [https://search.google.com/search-console](https://search.google.com/search-console) and add your blog

{% asset_img IMG-06.JPG %}

&nbsp;

{% asset_img IMG-07.JPG %}

&nbsp;

```html
<meta name="google-site-verification" content="YOUR_GOOGLE_VERIFICATION_TAG" />
```

Copy the html tag and paste it in gitorkouser/themes/landscape/layout/_partial/head.ejs and deploy the site. After deploy is successful click verify button to complete site verification. Goto sitemap and add the sitemap link to your blog

{% asset_img IMG-08.JPG %}

It will take an hour for the site to be indexed and show up on search results.

## Markdown

Learn markdown syntax [https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)