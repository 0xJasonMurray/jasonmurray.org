---
title: "How I managed website content with Hugo and text editors"
date: 2020-05-27T07:41:22-05:00
toc: false
images:
categories:
  - tech
tags: 
  - hugo
  - visualstudiocode
  - rsync
  - prowritingaid
  - vi
  - textastic
  - digitalocean
---

# Summary

- Create new post: `hugo new posts/mynewpost.md`
- Start development server: `hugo -D server`
- Edit content in your favorite editor: `vi posts/mynewpost.md`
- Check grammer in ProWritingAid
- Validate look and feel of site: `http://localhost:1313`
- Publish content: `rsync -av --delete ~jemurray/Documents/www-personal/current/jasonmurray.org/public/ shell.jasonmurray.org:/var/www/html`



# Details

## Tools

- Code editor: `vi`, `Visual Studio Code`, `Textastic`, etc.
- Static website generator: [Hugo](https://gohugo.io/)
- Hosting: [Digital Ocean](https://www.digitalocean.com/) VSP (small instance)


## Create new post

Everything starts out in either a terminal or Visual Code Studio (VSC).  In this post, I will switch between iTerm2 and VSC terminals and various editors depending on need.

Open a new terminal in VSC: `Terminal -> New Terminal`

![hugo new](/images/hugonew.png)

The `hugo new` command creates a new markdown template under the posts folder:

```
jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org $ hugo new posts/websiteflow.md
/Users/jemurray/Documents/www-personal/current/jasonmurray.org/content/posts/websiteflow.md created
jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org $ 
```

## Development Server

To avoid pushing draft data to the live website, Hugo has a built in webserver which renders content locally.  This allows the writer to validate the look and feel of the site before going live.  

To start the local web server open another terminal in VSC or iTerm and run:

```
jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org $ hugo -D server

                   | EN   
+------------------+-----+
  Pages            | 347  
  Paginator pages  |   0  
  Non-page files   |   0  
  Static files     |  74  
  Processed images |   0  
  Aliases          |   0  
  Sitemaps         |   1  
  Cleaned          |   0  

Total in 219 ms
Watching for changes in /Users/jemurray/Documents/www-personal/current/jasonmurray.org/{content,data,layouts,static,themes}
Watching for config changes in /Users/jemurray/Documents/www-personal/current/jasonmurray.org/config.toml
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

![hugo dev server](/images/hugodevserver.png)

## Automatic rebuild

The local development server stays running in the background scanning for changes to any files made under the Hugo directory.   When a file is changed, Hugo automatically rebuilds the entire site.

In addition to rebuilding the content, the server also reloads the website:

![reload website](/images/hugoreloadweb.png)

Side Note, if you look at the html code, the following line performs the website reload:

```
<script data-no-instant>document.write('<script src="/livereload.js?port=1313&mindelay=10"></' + 'script>')</script>
```


## Edit Content

Nothing special here.   Use your favorite editor.  

iCloud drives syncs files to all my devices.   I regularly switch between `vi`, `visual studio code`, and `Textastic` on mobile.

vi:

![hugo vi](/images/hugovi.png)

VSC:

![hugo vsc](/images/hugovsc.png)

Textastic:

![hugo textastic](/images/hugotextastic.PNG)


The entire layout looks like this on my desktop.  Editor is on top, browser in the background, file browsers for moving images around:

![hugo entire screen](/images/hugoentirescreen.png)

## Proofreading

[ProWritingAid](https://prowritingaid.com/Premium) checks the content before publishing.  

![hugo prowriting](/images/hugoprowriting.png)

## Publishing to the live site

I created a Bash `alias` to run the `rsync` command which uploads the website to Digital Ocean:

```
jemurray@mbp-2019:~ $ grep syncwebsite .bash_profile
alias syncwebsite="hugo && rsync -av --delete ~jemurray/Documents/www-personal/current/jasonmurray.org/public/ shell.jasonmurray.org:/var/www/html"
```

To upload the site, run: `syncwebsite`

```
jemurray@mbp-2019:~/www $ syncwebsite
building file list ... done
./
404.html
index.html
index.xml
sitemap.xml
...

sent 98247 bytes  received 21914 bytes  14136.59 bytes/sec
total size is 68175486  speedup is 567.37
```

## Validate

Validate the content on the live site:

![hugo main](/images/hugomain.png)
![hugo posts](/images/hugoposts.png)
