# hugoDebugTables

A [Hugo](https://gohugo.io) module that adds a partial and shortcode for displaying most variables available on a page in [Hugo](https://gohugo.io).

## Modern Hugo Only (as of 2021-03-20)

This module is built using assumptions that may require Hugo 0.81.0 or higher, and in any event that is the only version which it has been tested, at present.

## Acknowledgements

Idea and initial HTML source from
[zwbetz-gh/starter-hugo-debug-site debug-table](https://raw.githubusercontent.com/zwbetz-gh/starter-hugo-debug-site/master/layouts/partials/debug-table.html)

## Currently Debug Tables Only Added in "Development" Mode

The code is currently designed to only add the debug tables if running Hugo in "development" mode.
That means it will, by default, be added when using ``hugo server`` but not a regular build with ``hugo``.

## Usage

### In Your Theme or Site Code

#### Full Set of Tables

##### As a Partial in Layouts

To use the full list of available debug tables as a partial in your layouts, simply add:

```go
{{ partial "helpers/layouts/debug-tables/debug-tables-list" . }}
```

in the layout(s) used for the page(s) on which want the tables to appear.

##### As a Shortcode in Content Page

To use the full list of available debug tables as a shortcode in your content pages, simply add:

```go
{{< helpers/hugo-debug-tables >}}
```

to the page under your site's ``content/`` directory.

**NOTE:** This will only work if the content page in question actually gets rendered. (Many section layouts, for instance, do not render ``.Content`` and therefore this shortcode would not work with those layouts).

#### With Additional Parameters

Currently the only 'extra' parameter is 'expandPage'. If true many places
where a pages appears in the debug table you will be able to view detailed
Hugo variables for that page.

##### As a partial

```go
{{- partial "helpers/debug-tables/debug-tables-list" (dict "Page" .Page "Site" .Site "expandPage" true -}}
```

##### As a shortcode

```go
{{< helpers/hugo-debug-tables true >}}
```

##### As usual but with Site Param

In ``config.toml`` add

```toml
[params]
     debugExpandPage = "true"
```

#### Using Individual Tables

##### As a partial

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

##### As a shortcode

E.g.

```go
{{< helpers/hugo-debug-tables table="site" expandPage=true >}}
```

Where 'site' is one of ``page, section, file, site, taxonomy, hugo, os-stat``, and expandPage is optional (defaults to false).

Note that for the shortcode only ``.RawContent``, not ``.Content``, ``.Plain``, etc render for the current page (in the table produced by the shortcode).
### Adding the Code to Your Site

1. Get a copy of the code for, and switch to the directory for your site or theme.

#### Using Hugo Modules (preferred)

1. Initialize the Hugo module system: ``hugo mod init github.com/<your_user>/<your_project>`` (assuming you are using github, of course).
2. Import hugDebugTables in your ``config.html``
   ```toml
   [module]
     [[module.imports]]
        path = "github.com/danielfdickinson/hugoDebugTables"
   ```
3. Change back to the site directory
4. Get the module
   ```
   hugo mod get github.com/danielfdickinson/hugoDebugTables
   ```
5. Add the code (above) for using the tables to your source code.

#### Using downloaded copy of the module (e.g. Zip from Github)

1. Obtain a copy of the module e.g. ([a module Zip file from Github](https://github.com/danielfdickinson/hugoDebugTables/archive/master.zip))
2. Copy/extract the files in the archive into the root of your site or theme (archives contain /layouts and subdirectories under it).
3. Change back to the site directory

#### Run the Hugo Server

1. To test the result, run the local Hugo server (assumes you have a theme or layouts in your site)
   ```
   hugo server -b http://localhost:1313/
   ```
### Test CSS Styling

A test CSS file is available in ``static/css/hugo-debug-tables.css``. To use it place lines such as the following:

```go
{{ if eq hugo.Environment "development" -}}
<link rel="stylesheet" href="/css/hugo-debug-tables.css" />
{{- end }}
```

in the ``<head>`` section of your layout(s).
