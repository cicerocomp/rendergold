# RenderGold [![wercker status](https://app.wercker.com/status/3cb287c2c7570a2cf024eab2da6d7a14/s/ "wercker status")](https://app.wercker.com/project/bykey/3cb287c2c7570a2cf024eab2da6d7a14) [![GoDoc](https://godoc.org/github.com/yosssi/rendergold?status.png)](https://godoc.org/github.com/yosssi/rendergold)

Martini middleware/handler for parsing [Gold](http://gold.yoss.si/) templates and rendering HTML

## About Gold

[Gold](http://gold.yoss.si/) is a template engine for Go. Please visit [its GitHub repository](https://github.com/yosssi/gold) for more details.

## Usage

Here is an example of server-side Go code:

```go
package main

import (
	"github.com/go-martini/martini"
	"github.com/yosssi/rendergold"
)

func main() {
	m := martini.Classic()
	m.Use(rendergold.Renderer()) // reads "templates" directory by default

	m.Get("/", func(r rendergold.Render) {
		r.HTML(200, "top", nil) // parses "templates/top.gold"
	})

	m.Run()
}
```

Here is an example of `templates/top.gold`:

```gold
doctype html
html
  head
    title RenderGold
  body
    h1 Hello RenderGold!
```

This template will be converted to the following HTML:

```hmtl
<!DOCTYPE html>
<html>
  <head>
    <title>RenderGold</title>
  </head>
  <body>
    <h1>Hello RenderGold!</h1>
  </body>
</html>
```

## Options

`rendergold.Renderer` comes with a variety of configuration options:

``` go
// ...
m.Use(render.Renderer(render.Options{
  Directory: "templates", // Specify what path to load the templates from. Default is "templates".
  Func: template.FuncMap{AppHelpers}, // Specify helper function map for templates to access.
  Charset: "UTF-8", // Sets encoding for html content-types. Default is "UTF-8".
  HTMLContentType: "application/xhtml+xml", // Output XHTML content type instead of default "text/html".
  Asset: Asset, // Asset loads and returns the asset for the given name.
}))
// ...
```

## Load templates from binary data generated by go-bindata

You can load templates from binary data generated by [go-bindata](https://github.com/jteeuwen/go-bindata) by setting the `Asset` function (which is generated by go-bindata) to the `rendergold.renderer` via `rendergold.Options`.

```go
package main

import (
	"net/http"

	"github.com/martini-contrib/staticbin"
	"github.com/yosssi/rendergold"
)

func main() {
	m := staticbin.Classic(Asset)
	// Set the `Asset` function generated by go-bindata
	// to the `rendergold.renderer` via `rendergold.Options`.
	m.Use(rendergold.Renderer(rendergold.Options{Asset: Asset}))
	m.Get("/", func(r rendergold.Render) {
		r.HTML(
			http.StatusOK,
			"top",
			map[string]interface{}{
				"Title": "Gold - Template engine for Go",
			},
		)
	})
	m.Run()
}
```

## Sample package using RenderGold

* [yosssi/gold.yoss.si](https://github.com/yosssi/gold.yoss.si)

## Docs

* [GoDoc](https://godoc.org/github.com/yosssi/rendergold)
