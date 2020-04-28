# TND Netlify CMS Hugo Module

This module, in its phase 1, tends to simplify the construct of Netlify CMS lone config file and set up the Netlify CMS page wherever on the project.

By using Hugo's Data file system to store and maintain reusable data objects which can be imported as collections or fields, the module ease up on maintaining this wall of glyph that can quickly become your Netlify CMS' `config.yml`

## The Import statement

`import collection posts`
        |  type  | | file name

## Adding to your project

1. Create a page where your Netlify CMS dasboard should live.
2. Add the `netlifycms` layout.
2. Add the `netlifycms_config` output format to the page (alongside HTML):

```yaml
Title: Your CMS
layout: netlifycms
outputs:
  - HTML
  - netlifycms_config
```

Populate you `data/netlifycms` directory.

##  Example of the base config data file

```yaml
backend:
  name: git-gateway
  branch: easier-netlify-cms
publish_mode: editorial_workflow
# [ ... ]
slug:
  encoding: "ascii"
  clean_accents: true
  sanitize_replacement: "_"
collections:
- import collection pages #--> data/netlifycms/collections/pages.yaml
- import collection posts #--> data/netlifycms/collections/posts.yaml
```

## Example of a Collection data file

```yaml
name: "posts"
label: "Posts"
folder: "content/posts"
create: true
editor:
  preview: false
fields:
  - import field title #--> data/netlifycms/fields/title.yaml
  - import field authors #--> data/netlifycms/fields/authors.yaml
  - import field date #--> data/netlifycms/fields/date.yaml
  - import field images #--> data/netlifycms/fields/images.yaml
  - import field body #--> data/netlifycms/fields/body.yaml
  - {
      label: "Tags",
      name: "tags",
      widget: "list",
      field:
        {
          label: "Tag",
          widget: "string",
        },
    }
```

## Example of a Field data file

```yaml
label: "Title"
name: "title"
widget: "string"
```