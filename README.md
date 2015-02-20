# Buildpack: Go

This is a buildpack for [Go][go].

## Getting Started

Follow the guide at
<http://doc.scalingo.eu/languages/go>.

There's also a hello world sample app at
<https://github.com/Scalingo/sample-go-martini>.

## Example

```
$ ls -A1
.git
Godeps
Procfile
web.go

$ scalingo create my-go-app
$ git push scalingo master
...
-----> Fetching custom git buildpack... done
-----> Go app detected
-----> Installing go1.4.2... done
-----> Running: godep go install -tags paas ./...
-----> Discovering process types
       Procfile declares types -> web

-----> Compressing... done, 1.6MB
-----> Launching... done, v4
       https://polar-waters-4785.herokuapp.com/ deployed to Heroku
```

This buildpack will detect your repository as Go if it contains a `.go` file.

The buildpack adds a `paas` [build constraint][build-constraint],
to enable Platform as a Service specific code. See the [App Engine build constraints article][app-engine-build-constraints]
for more.

[go]: http://golang.org/
[quickstart]: http://doc.scalingo.com/languages/go/
[build-constraint]: http://golang.org/pkg/go/build/
[app-engine-build-constraints]: http://blog.golang.org/2013/01/the-app-engine-sdk-and-workspaces-gopath.html

## .godir and Godeps

Early versions of this buildpack required users to
create a `.godir` file in the root of the project,
containing the application name in order to build the
project. While using a `.godir` file is still supported,
it has been deprecated in favor of using
[godep](https://github.com/tools/godep) in your project to
manage dependencies, and including the generated `Godep`
directory in your git repository.

## Using with cgo

This buildpack supports building with C dependencies via
[cgo](http://golang.org/cmd/cgo/). You can set config vars to specify
CGO flags to, e.g., specify paths for vendored dependencies. E.g., to
build [gopgsqldriver](https://github.com/jbarham/gopgsqldriver), add
the config var `CGO_CFLAGS` with the value
`-I/app/code/vendor/include/postgresql` and include the relevant
Postgres header files in `vendor/include/postgresql/` in your app.

## Setting the version at build time

If you set the `GO_GIT_DESCRIBE_SYMBOL` to the name of a
string variable, it will be set at build time to the
output of `git describe --tags --always`. This lets you
access the commit id or tag in your app. For example, in
your `main.go`:

```go
package main

var version string
```

To set this variable at build time, set the config var:

```bash
$ scalingo env-set GO_GIT_DESCRIBE_SYMBOL=main.version
```
