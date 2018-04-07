---
title: Connections
permalink: querying/connections
---

### Connections

Connections can be used when the more simplistic `entries` type fields don't provide the data you need. The best example of this is when you need to fetch pagination data. Using the `entries` field you may try this,

```graphql
{
    entries(section: news, limit: 10) {
        title
        url
    }
}
```

That would work and return the first 10 news entries, but you would have no way of knowing _how many_ results there actually are. In a template you've have access to `.count` but GraphQL is much more strict with its types. We can't add properties on to a collection because it is a vanilla array, and nothing more, in the response. Therefore, the convention of wrapping collections in to a Connection arose.

A connection is a convention that implies "there's a collection of data inside here. You can get the data as well as meta data about the collection." In our case that means we can get the `totalCount` as well as pagination info. You could write this as,

```graphql
{
    entriesConnection(section: news) {
        edges {
            node {
                title
                url
            }
        }
        totalCount
        pageInfo {
            totalPages
            hasNextPage
            hasPreviousPage
        }
    }
}
```