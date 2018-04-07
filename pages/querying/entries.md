---
title: Entries
permalink: querying/entries
---
### Querying

You will typically query entries out of CraftQL via the top level `entries` field.

```graphql
{
    entries {
        id
    }
}
```

`entries` accepts the same arguments that `craft.entries` does in a template.

So, `.section("news")` would translate to `entries(section:news)` in CraftQL. Here's a sample query exposing a number of CraftQL's built in fields,

```graphql
{
    entries(section: news, order: "postDate desc", limit: 10, search: "body: breaking") {
        title
        body
    }
}
```