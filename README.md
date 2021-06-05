# Hugo Debug Tables

A [Hugo](https://gohugo.io) module that adds a partial and shortcode for displaying most variables available on a page generated by Hugo.
See [License](https://git.wildtechgarden.ca/danielfdickinson/hugoDebugTables/src/branch/master/LICENSE) for permissions on use.

## Modern Hugo Only (as of 2021-03-20)

This module is built using assumptions that may require Hugo 0.81.0 or higher, and in any event those are the only versions on which it has been tested, at present.

## Acknowledgements

Idea and initial HTML source from
[zwbetz-gh/starter-hugo-debug-site debug-table](https://github.com/zwbetz-gh/starter-hugo-debug-site/blob/master/layouts/partials/debug-table.html)

## Using in Your Theme or Site
### Full Set of Tables

#### As a Partial in Layouts

To use the full list of available debug tables as a partial in your layouts, simply add:

```go
{{ partial "helpers/layouts/debug-tables/debug-tables-list" . }}
```

in the layout(s) used for the page(s) on which want the tables to appear.

#### As a Shortcode in a Content Page

To use the full list of available debug tables as a shortcode on a page in your content pages, simply add:

```go
{{\< helpers/hugo-debug-tables >}}
```

to the page on which you want the table, under your site's ``content/`` directory.

**NOTE:** This will only work if the content page in question actually gets rendered. (Many section layouts, for instance, do not render ``.Content`` and therefore this shortcode would not work with those layouts).

### With Additional Parameters

Currently the only 'extra' parameter is 'expandPage'. If true many places
where a pages appears in the debug table you will be able to view detailed
Hugo variables for that page.

**WARNING: expandPage is SLOW!!!**

expandPage in a partial or site-wide param means that for every page on the site, the entire site's metadata gets processed. This can **exponentially** increase your build times. Use sparingly and knowing you may need to cancel the build due to time or memory and cpu constraints. In addition be aware this can greatly increase the size of your pages, as on every page the debug table will include things like the content from every page on the site, often more than once (e.g. for the .Content table entry in the Page table).

#### As a partial

```go
{{- partial "helpers/debug-tables/debug-tables-list" (dict "Page" .Page "Site" .Site "expandPage" true) -}}
```

#### As a shortcode

```go
{{\< helpers/hugo-debug-tables true >}}
```

#### Using partial without expandPage but adding a Site Param

In ``config.toml`` add

```toml
[params]
     debugExpandPage = "true"
```

### Using Individual Tables

#### As a partial

Each of the tables (page, section, file, site, taxonomy, hugo, os-stat) expects the same context: a dictionary (dict) with following keys:
   * Page
   * basePage
   * Site
   * baseSite
   * expandPage

So, for example to emit the site table, one would use:
```go
{{ partial "helpers/debug-tables/tables/site" (dict "Site" site "baseSite" $baseSite "Page" $curPage "basePage" $basePage "expandPage" $expandPage) }}
```

#### As a shortcode

E.g.

```go
{{\< helpers/hugo-debug-tables table="site" expandPage=true >}}
```

Where 'site' is one of ``page, section, file, site, taxonomy, hugo, os-stat``, and ``expandPage`` is optional (defaults to false).

Note that for the shortcode, only ``.RawContent`` renders, not ``.Content``, ``.Plain``, etc for the current page (in the table produced by the shortcode).
## Adding the Code to Your Site or Theme

1. Get a copy of the code for, and switch to the directory for your site or theme.

### Using Hugo Modules (preferred)

**NB** Due to limitations of Hugo modules when it comes to repo locations this is a bit of a mess at the moment; it will be cleaned up sometime "Real Soon Now" for this repo, as moving and renaming has caused some grief.

1. Initialize the Hugo module system: ``hugo mod init github.com/<your_user>/<your_project>`` (assuming you are using github, of course).
2. Import hugDebugTables in your ``config.html``
   ```toml
   [module]
     [[module.imports]]
        path = "gitea.wildtechgarden.ca/danielfdickinson/hugoDebugTables"
   ```
3. Change back to the site directory
4. Get the module
   ```
   hugo mod get gitea.wildtechgarden.ca/danielfdickinson/hugoDebugTables
   ```
5. Add the code (above) for using the tables to your source code.

### Using downloaded copy of the module (e.g. Zip from the Git repo)

1. Obtain a copy of the module e.g. ([a module Zip file from the Git repo](https://gitea.wildtechgarden.ca/danielfdickinson/hugoDebugTables/archive/master.zip))
2. Copy/extract the files in the archive into the root of your site or theme (archives contain /layouts and subdirectories under it).
3. Change back to the site directory

### Run the Hugo Server

1. To test the result, run the local Hugo server (assumes you have a theme or layouts in your site)
   ```
   hugo server -b http://localhost:1313/
   ```

## Currently Debug Tables Only Added in "Development" Mode

The code is currently designed to only add the debug tables if running Hugo in "development" mode.
That means it will, by default, be added when using ``hugo server`` but not a regular build with ``hugo`` (if you call the partial or shortcode, of course).

### Change Debug Table from Development Only

#### To add to production builds

In ``config.toml`` add

```toml
[params]
     debugTableEnvironment = ["development","production"]
```

#### To never include in builds

```toml
[params]
     debugTableEnvironment = [""]
```

**NB** The ``""`` is required because an empty slice (or no param) defaults to ``["development"]``

#### You can also use custom environments

In ``config.toml`` add

```toml
[params]
     debugTableEnvironment = ["custom1","custom2"]
```

And of course if you wanted you could add ``"development"`` and/or ``"production"``

## Test CSS Styling

A test CSS file is available in ``static/css/hugo-debug-tables.css``. To use it place a line such as the following:

```go
{{- partial "helpers/debug-tables/debug-head-snippet" . -}}
```

in the ``<head>`` section of your layout(s).

### A Note on Navigation

Details (disclosure elements) embedded inside a another table create a new layer above the current table when opened. To get rid of the new layer and close the table, close the details (disclosure) element that appears above the table or other disclosure content.

## CSS Classes Available for Styling the Debug Tables

Two types of classes available:

   * Element classes (e.g. details-debug-hugo)
      * By actual use of element not the HTML element type (e.g. ``details``, ``list``, ``table-cell``) — sometimes an \<li> might be used with ``details`` when
      there is no additional information, but the item is a part of a list of details.
      * May include a hyphen in the element type part
      * Always end in ``-debug-hugo`` to denote they are 'debug table' classes
   * Category/Purpose Classes
      * E.g. ``debug-pages-hugo`` which is wrapped around HTML for page lists/tables
      * Always begin with ``debug`` and end in ``hugo``. The middle is a single word (no hyphens).

### Element Classes

| Class                       | Description                                 |
|-----------------------------|---------------------------------------------|
| code-debug-hugo             | Added to any element intended to show source code or content (as source) |
| details-debug-hugo          | Added to any element that can contain additional details with a disclosure widget (even of not actually currently containing additional detail, but could, if ``expandPage`` was true, for instance) |
| list-debug-hugo             | Added to any list container element (e.g. \<ul>) |
| list-item-debug-hugo        | Added to any list item that doesn't use a disclosure element (.eg. \<li>) |
| pre-debug-hugo              | Added to any element to be used as preformatted text |
| section-debug-hugo          | Added to any master wrapper element (e.g. \<section>) |
| summary-debug-hugo          | Added to any element which contains the summary for a ``details-debug-hugo`` element |
| table-debug-hugo            | Added to any table |
| table-row-debug-hugo        | Added to any table row |
| table-cell-head-debug-hugo  | Added to any table heading cell (e.g. \<th>) |
| table-cell-debug-hugo       | Added to any regular table cell (e.g. \<td>) |

### Category/Purpose Classes

| Class                       | Description                                 |
|-----------------------------|---------------------------------------------|
| debug-acknowledgements-hugo | Added to wrapper around acknowledgements table |
| debug-content-hugo          | Added to any top-level wrapper around page content (source) displayed in a debug table |
| debug-current-hugo          | Added to an element wrapping a current page or site |
| debug-menus-hugo            | Added to an element wrapping a list of menus |
| debug-pages-hugo            | Added to any top-level wrapper around a list of .Pages |
| debug-sites-hugo            | Added to any top-level wrapper around a list of .Sites |
| debug-variables-hugo        | Added to top-level wrapper \<section> element when displaying all tables |
