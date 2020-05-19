# TND Netlify CMS Hugo Module

This module try to simplify the construct of Netlify CMS lone config file. It also sets up the Netlify CMS page wherever on the project with minimal user intervention.

In order to be able to reuse data objects inside the lone NetlifyCMS `config.yaml` file, this module makes use of Hugo's data file to store said data objects and allow them to be recursively imported in the configuration file.

## The Import statement
``` 
import collection posts
       |  type  | | file name
```
## Adding to your project


1. Add this module's path to your `module.imports` config array
```
module:
  imports:
    - path: github.com/theNewDynamic/hugo-module-tnd-netlify-cms
```
1. Create a page where your Netlify CMS dasboard should live.
2. Add the `netlifycms` layout to it through Front Matter.
3. Add the `netlifycms_config` output format to it through Front Matter (alongside HTML):
```yaml
Title: Your CMS
layout: netlifycms
outputs:
  - HTML
  - netlifycms_config
```
4. Add your Netlify config data in `data/netlifycms/config.yaml` and use (or not) `import` statements.
5. Add the data files matching your imports statements to the `data/netlifycms` dir under `/collections` and `/fields`

##  Example of the base config data file

```yaml
# data/netlifycms/config.yaml
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
# data/netlifycms/collections/posts.yaml
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
# data/netlifycms/fields/title.yaml
label: "Title"
name: "title"
widget: "string"
```