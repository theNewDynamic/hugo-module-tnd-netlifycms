# TND NetlifyCMS Hugo Module

This module try to simplify the construct of Netlify CMS lone config file. It also sets up the Netlify CMS page wherever on the project with minimal user intervention.

In order to be able to reuse data objects inside the lone NetlifyCMS `config.yaml` file, this module makes use of Hugo's data file to store said data objects and allow them to be recursively imported in the configuration file.

## Requirements

Requirements:
- Go 1.14
- Hugo 0.61.0

## Installation

If not already, [init](https://gohugo.io/hugo-modules/use-modules/#initialize-a-new-module) your project as Hugo Module:

```
$: hugo mod init {repo_url}
```

Configure your project's module to import this module:

```yaml
# config.yaml
module:
  imports:
  - path: github.com/theNewDynamic/hugo-module-tnd-netlifycms
```

## Usage

### Adding to your project

1. Add this module's path to your `module.imports` config array
```
module:
  imports:
    - path: github.com/theNewDynamic/hugo-module-tnd-netlifycms
```
1. Create a page where your Netlify CMS dasboard should live.
2. Add the `netlifycms` type to it through Front Matter.
3. Add the `netlifycms_config` output format to it through Front Matter (alongside HTML):
```yaml
Title: Your CMS
type: netlifycms
outputs:
  - HTML
  - netlifycms_config
```
4. Add your Netlify config data in `data/netlifycms/config.yaml` and use (or not) `import` statements.
5. Add the data files matching your imports statements to the `data/netlifycms` dir under `/collections` and `/fields`

### Import and Extend data sets

NetlifyCMS uses yaml extensively. In order to avoid a lot of copy/paste the module allow to import a data set stored in `data/netlifycms/**/*.yaml`

It's also possible to extend the data found in the file.

See example below of usage.

### Example of the base config data file

```yaml
# data/netlifycms/config.yaml
backend:
  name: git-gateway
  branch: easier-netlify-cms
collections:
- import collection events #--> data/netlifycms/collections/pages.yaml
- import collection posts #--> data/netlifycms/collections/posts.yaml
- import: collection posts
  extend:
    name: updates
    label: News Updates
    folder: content/updates
```

Above, the Events and Posts collections are stored in the mentioned data file. As we import the data as is, we can use a simple `import` statement.

For updates, we need almost the same settings and fields as the Post collection, except for a few keys that need to be overwritten.
We can use a map with two keys:
  - import: For the statement
  - extend: The data which will be added/merged on top of the
   data set.


### Example of a Collection data file

```yaml
# data/netlifycms/collections/posts.yaml
name: "events"
label: "Events"
folder: "content/events"
create: true
fields:
  - import field title #--> data/netlifycms/fields/title.yaml
  - import field authors #--> data/netlifycms/fields/authors.yaml
  - import field date #--> data/netlifycms/fields/date.yaml
  - import field images #--> data/netlifycms/fields/images.yaml
  - import field body #--> data/netlifycms/fields/body.yaml
  - import: field time #--> data/netlifycms/fields/time.yaml
    extend:
      name: start_time
      label: Start
  - import: field time
    extend:
      name: end_time
      label: End
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

Above we can see that regular handwritten data and import statements, as well as extending data can perfectly coexist among the same array.

It is convenient to create a "base" field like `time.yaml` on which to extend special data. This way, the two fields storing a "Start" and an "End" time can use the same base of settings for date and time formating while retaining their own `name` and `label`.

### Examples of a Field data file

```yaml
# data/netlifycms/fields/title.yaml
label: "Title"
name: "title"
widget: "string"
```

```yaml
# data/netlifycms/fields/time.yaml
label: "Time"
name: "time"
widget: "datetime"
default: ""
dateFormat: "DD.MM.YYYY" # e.g. 24.12.2021
timeFormat: "HH:mm" # e.g. 21:07
format: "LLL"
pickerUtc: false
```

### Settings

Module settings are set as part of the shared `data/netlifycms/config.yaml` file under the reserved `tnd_netlifycms` map key.

Available settings are:
 - cms_version (default: `^2.0.0`): Semantic versioning string to be used when calling CDN endpoint.

## Scripts

The module faciliate the addiont of extra Netlify CMS scripts for WidgetPreviews, CollectionPreviews etc...

By default the module prints the content of any `assets/netlifycms.js` file found inside a `<script>` tag.

For more control, user should create its own `layouts/partials/tnd-netlifycms/scripts.html` partial and load the project's custom scripts there.

### Custom Scripts Partial example: 

In this example, we use the load the project's stylesheet for the Articles previews and fire the Custom Preview afterwards, using the project's own classes.

```
{{ $style := false }}
{{ with resources.Get "css/style.css" }}
  {{ $style = . | resources.PostCSS }} 
{{ end }}
<script>
  {{ with $style }}
  CMS.registerPreviewStyle("{{ $style.Permalink }}");
  {{ end }}
  var PostPreview = createClass({
    render: function() {
      var entry = this.props.entry;
      return h('div', {"className": "px-4"},
        h('h1', {"className": "text-5xl font-normal pt-10 mb-16"}, entry.getIn(['data', 'title'])),
        h('div', {"className": "my-4 user-content"}, this.props.widgetFor('body'))
      );
    }
  });
  CMS.registerPreviewTemplate("articles", PostPreview);
</script>
```

## theNewDynamic

This project is maintained and loved by [thenewDynamic](https://www.thenewdynamic.com).