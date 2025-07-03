---
title: "Using markdown and pandoc to write documentation"
date: 2020-07-01T15:59:07-05:00
toc: false
images:
categories:
  - tech
tags: 
  - pandoc
  - markdown
---

For those people who would rather write documentation and worry less about what system your company wants you to store it in, consider using a light weight language such as `markdown` paired with the conversion tool [pandoc](https://pandoc.org/).  This combination allows the writer to focus on the content and publish the final copies in any format `pandoc` can [convert](https://pandoc.org/MANUAL.html#option--to) to such as pdf, html, epub, wiki, or docx.


Take the following `markdown` input:

```
jemurray@mbp-2019:~ $ cat example.md
# Example Heading

With some example text.

## Example subheading

With a bulleted list:

- Item 1
- Item 2
- Item 3
- Item 4

> Quoted text could go here.

A inline image: ![Alt Text](/path/to/image.jpg)

A link to an external site: [Jason Murray](https://jasonmurray.org)

All this created with the lightweight text editor `vi`.
```

By using pandoc `markdown` can be converted to html:

```
jemurray@mbp-2019:~ $ pandoc example.md -f markdown -t html
<h1 id="example-heading">Example Heading</h1>
<p>With some example text.</p>
<h2 id="example-subheading">Example subheading</h2>
<p>With a bulleted list:</p>
<ul>
<li>Item 1</li>
<li>Item 2</li>
<li>Item 3</li>
<li>Item 4</li>
</ul>
<blockquote>
<p>Quoted text could go here.</p>
</blockquote>
<p>A inline image: <img src="/path/to/image.jpg" alt="Alt Text" /></p>
<p>A link to an external site: <a href="https://jasonmurray.org">Jason Murray</a></p>
<p>All this created with the lightweight text editor <code>vi</code>.</p>
```

Plain text output:

```
jemurray@mbp-2019:~ $ pandoc example.md -f markdown -t plain
Example Heading

With some example text.

Example subheading

With a bulleted list:

-   Item 1
-   Item 2
-   Item 3
-   Item 4

  Quoted text could go here.

A inline image: [Alt Text]

A link to an external site: Jason Murray

All this created with the lightweight text editor vi.
```

Microsoft docx output:

```
jemurray@mbp-2019:~ $ pandoc example.md -f markdown -t docx -o example.docx
jemurray@mbp-2019:~ $ open example.docx
```

![docx example output](/images/docxexample.png)

