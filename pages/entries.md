---
permalink: entries
---

### Getting Started

To ensure that CraftQL is working you may want to query the `helloWorld` field. You could do this via CURL like so,

```bash
$ curl -H "authorization: bearer {token}" -H "Content-type: application/json" -d '{"query":"{ helloWorld }"}' http://localhost/api
```

Note: in order to make this work you have to create a Token in the CraftQL settings. Once generated you can pass the token through an `authorization` header. 

### Querying

You will typically query entries out of Craft via the top level `entries` field.

```graphql
{
    entries {
        id
    }
}
```

`entries` accepts similar arguments as `craft.entries` does in the template. So `.section("news")` would translate to `entries(section:news)` in CraftQL. Here's a sample query exposing a number of Craft's built in fields,

```graphql
{
    entries(section: news, order: "postDate desc", limit: 10, search: "body: breaking") {
        title
        body
    }
}
```