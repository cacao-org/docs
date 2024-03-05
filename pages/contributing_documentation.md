---
title: Writing Documentation
keywords: documentation
last_updated: Mar 4, 2024
tags: [documentation]
summary: "Writing Documentation"
sidebar: home_sidebar
permalink: contributing_documentation.html
folder: 
---

# Editing existing pages

This documentation uses markdown syntax, with enhancements.
See [Jekyll theme instructions](https://idratherbewriting.com/documentation-theme-jekyll/index.html) for details.

Use the "Edit me" button at the top of the page (below title), or edit the .md file in a local copy of the documentation, following usual git practices.

# Building the documentation page

## Public online pages

Pages are automatically built. Allow for a couple minutes after pushing new content.

## Local copy

This documentation uses Jekyll.
Clone the repository and built locally. For example:

~~~bash
bash
# build and serve on local host
bundle exec jekyll serve
~~~

See the [Jekyll theme instructions](https://idratherbewriting.com/documentation-theme-jekyll/index.html) for details and instructions to install required software.



# Files and Directories

## Pages content

Individual pages are in `pages/` directory, and are themselves organized in subdirectories. Each file must start with a header containing title, keywords, tags, summary, sidebar (which sidebar should be displayed on the left for this page), and permalink (html file name that will be built). Check any of the existing pages for an example if creating a new page.

The pages are built from .md sources to .html content in the `_site/` directory.
{% include warning.html content="
Do not edit content in the `_site/` directory. Content should be edited in the `pages/` directory.
" %}

## Navigation

The theme uses two navigation menus: one at the top of the page (topnav), and one on the left (sidebar).

The top menu is defined in file `_data/topnav.yml`.

There are multiple versions of the sidebar, defined in `_data/sidebar/` directory. The sidebar to be used in each page is specified in its header (`sidebar` entry).




{% include links.html %}
