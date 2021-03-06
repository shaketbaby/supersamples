![logo](https://raw.github.com/rprieto/supersamples/master/logo.png)

Documentation and samples for your Node.js RESTful API

[![NPM](https://nodei.co/npm/supersamples.png)](https://www.npmjs.org/package/supersamples)

[![Travis CI](https://api.travis-ci.org/rprieto/supersamples.png)](https://travis-ci.org/rprieto/supersamples) [![Dependency Status](https://david-dm.org/rprieto/supersamples.png?theme=shields.io)](https://david-dm.org/rprieto/supersamples) [![devDependency Status](https://david-dm.org/rprieto/supersamples/dev-status.png?theme=shields.io)](https://david-dm.org/rprieto/supersamples#info=devDependencies)

`supersamples` is a [Mocha](https://github.com/visionmedia/mocha) reporter that understands [Supertest](https://github.com/visionmedia/supertest) to generate reliable and up-to-date API samples. In a nutshell:

- define concrete request/response examples in your test suite
- if you need to, use mocks to make sure you fully control the API responses
- add a few explanations in Markdown
- choose from a few output formats
- get high-level API documentation that's always up-to-date!

*Works with any Node.js `http.Server`, like [Express](https://github.com/visionmedia/express) or [Restify](https://github.com/mcavage/node-restify)*

## What will my tests look like?

Nothing special! Simply use `supertest` in your test suite, and `supersamples` will generate the request/response documentation for you!

```coffee
it '''
# Get list of sports
- list is ordered alphabetically
- doesn't return sports with no active competitions
''', (done) ->

  request(server)
    .get('/sports')
    .set('Accept', 'application/json')
    .expect(200)
    .expect('Content-Type', /json/)
    .expect(
      sports: [
        { id: 1, name: 'Soccer' }
        { id: 2, name: 'Tennis' }
      ]
    )
    .end(done)
```

## What will the docs look like?

`supersamples` comes with several renderers built-in:

- `html` generates a multi-page static HTML website
- `markdown` to generate a single Markdown page you can easily upload to Github
- `json` to generate JSON metadata you can process later

See a live example of the HTML output [over here](http://rprieto.github.io/supersamples).

<a href="http://rprieto.github.io/supersamples"><img src="https://raw.github.com/rprieto/supersamples/master/thumbnail.png" /></a>

## How do I set it up?

```
npm install supersamples --save-dev
```

Have a look at the [example folder](http://github.com/rprieto/supersamples/blob/master/example) to get started. You can add tests to the usual `test` folder, or keep them separate if you want. Simply run Mocha with the provided reporter:

```bash
./node_modules/.bin/mocha --reporter supersamples path/to/tests
```

You also need to specify documentation options in a **supersamples.opts** file at the root. This file has to be valid `JSON`, but also supports comments:

```js
{
  
  // Base URL for the API
  "baseUrl": "http://my-api.com",

  // One or more renderering modes
  // And their associated options
  "renderers": {

    "<name>": { ... }
    "<name>": { ... }

  }

}
```

See each renderer for the set of available options:

- [HTML](https://github.com/rprieto/supersamples/tree/master/lib/renderers/html/README.md)
- [JSON](https://github.com/rprieto/supersamples/tree/master/lib/renderers/json/README.md)
- [Markdown](https://github.com/rprieto/supersamples/tree/master/lib/renderers/markdown/README.md)

## What goes in the docs?

**The navigation**

In the HTML renderer, the first few levels of `describe()` statements make up the navigation sidebar.

**Your markdown content**

The `it()` statements can contain valid Markdown, which make up the description of each example.

**The requests**

- The request headers, including custom ones. However it excludes typically irrelevant headers for the context of documentation (`accept-encoding: gzip, deflate`, `host: http://localhost:1234`...).
- The request payload & attachements.

**The responses**

- The response status code, regardless of any `expect()`.
- The response headers, but only if they were mentioned in `expect()`. The reason is that many frameworks will add dozens of default headers, which could seriously clutter the docs.
- The actualy response body, regardless of any `expect()`. Note that even if they don't affect the docs, expectations are checked during the generation process. We 100% recommend that you add some to give extra confidence that the HTTP response are correct. 

## What doesn't it do?

`supersamples` DOES NOT provide a way to describe every path or query string parameter. It's meant to give you reliable but low-cost API samples. If you want a very detailled API description, you might like other tools better:

&nbsp;&nbsp;&nbsp;&nbsp;- tools like [Apiary](http://apiary.io) or [ApiDoc](http://apidocjs.com) let you document your API in text-format (for example Markdown or JavaScript comments). Just remember to keep these up to date!

&nbsp;&nbsp;&nbsp;&nbsp;- tools like [Swagger](http://developers.helloreverb.com/swagger/) provide a JavaScript API to define your routes. It can generate docs that are always up-to-date, if you don't mind using their syntax instead of vanilla Express or Restify.
