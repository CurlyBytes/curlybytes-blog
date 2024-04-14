<!--
author: Francisco Abayon
head: https://www.gravatar.com/avatar/dd90d96a247f981286ae0092abc026ba.jpg
date: 2024-04-01    
title: Codeigniter 3 - Static Site Generator
tags: codeigniter3, staticsitegenerator, markdown, php, twig, cdn, contentmanagementsystem, framework, wordpress, saas, medium, devto, blog, hugo, jekyll, azuredevops, mvp, docker, container
images: blog/img/codeigniter3-love-markdown-darkmode.jpg
category: Blog Techstack
status: publish
summary: How do you even manage to create static site generator with a codeigniter 3 in this current trends in 2024
-->
## Motivation
In my early days of carreer as a software engineer, im astonish on the blog article i read on each time a unique technical problem with its corresponding solution how to solve it, whether a giant tech blog industry upto solo content creator of blog. With that I start to create my own blog.

## Problem Context
You may wonder, why i never opted to prebuilt CMS(Content Management System) tools like <a href="https://wordpress.com/" target="_blank" rel="nofollow">Wordpress</a>, or SAAS(Software As A Service) such as <a href="https://dev.to/" target="_blank" rel="nofollow">Dev.to</a>. I want to have something in between, you may recommend to me to use a static site generator such as <a href="https://jekyllrb.com/" target="_blank" rel="nofollow">Jekyll</a> or <a href="https://gohugo.io/" target="_blank" rel="nofollow">Hugo</a> but the thing is i want to create my own static site generate because:
* I need to explore outside of my field and additionanl skillset due to meet my expectation as a T-Shape Developer
* I am a DevOps Architect in my latest career, i want to embed a workflow automation in this aspect
* For fun and experiment in the field of content creation 

## User Acceptance Criteria
With the three use case state above, this are the checklist to proceed and its corresponding requirements as follows:
* Must have a niche techstack 
* Must be a static blog 
* The content should be in Markdown format
* Must have themification
* Must be integrated to CICD pipeline such as Azure DevOps
* Hosted in Static Assets services such as Azure CDN
* Minimum Viable Product to make the blog up and running
* SEO Compliant

In the future iterration, it is nice to have:
* Must use the decent bare minimum framework for techstack for static site generator
* It must be a dynamic data imports each time a new content create for vlog, blog, project, and skills
* Integration with APIs to <a href="https://github.com/" target="_blank" rel="nofollow">Github</a>
* Integration with APIs to <a href="https://dev.to/" target="_blank" rel="nofollow">Dev.to</a>
* Integration with APIs to <a href="https://www.linkedin.com/" target="_blank" rel="nofollow">LinkedIn Project</a>
* Integration with APIs to <a href="https://www.youtube.com/" target="_blank" rel="nofollow">Youtube</a>
* Integration with APIs to <a href="https://learn.microsoft.com/en-us/users/franciscoabayon-6311/" target="_blank" rel="nofollow">Microsoft Certification</a>
* Integration with APIs <a href="https://www.udemy.com/" target="_blank" rel="nofollow">Udemy</a>
* Working Contact Forms for a static website
* Integrate security such as captcha 
* Implement honeypots to all forms
* A niche theme for techy people
* Everthing must be on serverless queries both schedule post and event trigger updates
* Have navigation to next blog post
* Must have a search field and index the content of a static website
* Track analytics of the page traffic
* Must follow full blown structure of post such as categories, tags, authors, navigation
* Cross posting the page without hampering the SEO such as dev.to, medium and hashnode
* Integration with DevSecOps workflow
* Comment system on staic website
* SEO Optimization such as search engine crawl and backlinking
* Custom editor for markdown editing
* Syncing to a headless cms

## Pathfinding Solution
The four requirements are *Must have a niche techstack*, *Must be a static blog*, *The content should be in Markdown format*, and *Must have themification*, these are the desicion factor on what is the tech stack to be use and use my first php framework when i was working on company as a software engineer with a <a href="https://codeigniter.com/userguide3/index.html" target="_blank" rel="nofollow">CodeIgniter 3</a> because there is somebody already encounter this use case. Credits to  <a href="https://github.com/jockchou" target="_blank" rel="nofollow">Jockhou</a> a codeigniter markdown blog that has a similar funtion with a basic <a href="https://wordpress.com/" target="_blank" rel="nofollow">Wordpress</a> functionality such as as tags, categories, filter by date and even search in the form by using a <a href="https://www.markdownguide.org/" target="_blank" rel="nofollow">Markdown</a> as a content of the blog post.

So we <a href="https://github.com/CurlyBytes/gitblog" target="_blank" rel="nofollow">fork</a> it first to support and credit the original author, then change the settings of the repo to become a template repository in order create a new repo project as base <a href="https://github.com/CurlyBytes/curlybytes-blog" target="_blank" rel="nofollow">project repository</a>.
![Alt text](img/template-gitblog.png "Forked Gitblog is change to template repository so that it can be resuse as base project")

Then we need to run this legacy framework and modernizing it by doing containerization with a docker compose script and since this is php, let utilize with <a href="https://phpdocker.io/" target="_blank" rel="nofollow">PHP Docker</a> and modify it to put in the __script__ folder and change the settings accordinly to PHP 7.4. Then put the phpdocker folder to a script folder and fix the volume mounting of the container. I also add another webservice for static site mount to **_site** folder so that the static html file run on different web server.

```docker-compose

###############################################################################
#                          Generated on phpdocker.io                          #
###############################################################################
---
version: "3.1"
services:
    webserver:
        image: 'nginx:${NGINXVERSION}'
        container_name: ${APPNAME}_webserver
        working_dir: /application
        volumes:
            - '.:/application'
            - './scripts/phpdocker/nginx/nginx.conf:/etc/nginx/conf.d/default.conf'
            - ./app/logs/nginx:/var/logs/nginx
        ports:
            - '${WEBSERVERPORT}:80'


    php-fpm:
        container_name: ${APPNAME}_php
        build: scripts/phpdocker/php-fpm
        working_dir: /application
        volumes:
            - '.:/application'
            - './scripts/phpdocker/php-fpm/php-ini-overrides.ini:/etc/php/7.4/fpm/conf.d/99-overrides.ini'
        environment:
            PHP_IDE_CONFIG: "serverName=Docker"   

    web:
        image: nginx:alpine
        ports:
        - "80:80"
        volumes:
        - ./_site:/usr/share/nginx/html
        
```

Lastly lets change the settings and create the config.yml of the blog themification according to the original authors readme file, and fix some errors in twig in parsing it since this is the last commit is __2017__ code. And i also add my <a href="https://github.com/" target="_blank" rel="noopener">Introduction Blog</a> and this Article too. Then perform the static site export of the gitblog controller to generate the static html with its corresponding static assets


``` bash
php index.php gitblog exportsite
```
