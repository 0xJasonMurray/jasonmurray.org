---
title: "Archetypes in Hugo for new post templates"
date: 2020-06-17T17:15:26-05:00
toc: false
images:
categories:
  - tech
tags: 
  - hugo
  - archetype
---

[Hugo archetypes](https://gohugo.io/content-management/archetypes/) provide a templating mechanism when creating a new post with the command: `hugo new path/file.md`.

By default, Hugo uses a template that looks similar to this:

```
jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org/themes/hermit/archetypes $ cat posts.md
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: true
toc: false
images:
tags:
  - untagged
---
```



To change the default template, create the file `$hugoroot/archetypes/posts.md`.  Do not edit the archetype file in the individual themes directory, it will be replaced when the theme is updated.  In this example, I added a category field:

```
jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org $ cat archetypes/posts.md
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: true
toc: false
images:
categories:
  - nocat
tags:
  - untagged
---
```