---
title: Introduction
permalink: introduction
---

### Getting Started

CraftQL provides a number of advanced features to query, mutate, and interact with your Craft data. Those features are all exposed through a standard GraphQL API. If this is your first time working with GraphQL open up the Craft admin UI and click on CraftQL in the navigation. From there you'll be presented with two panes, an input pane and an output pane. Type the following in to the left half, input pane,

```graphql
{
    helloWorld
}
```

And with that you've just interacted with GraphQL! From here your path may go in a number of directions, such as:

- [Querying entries](#)
- [Mutating entries](#)
- [Developing for CraftQL](#)

### Remote Access

Typically you won't be typing GraphQL queries directly in to the Craft admin. Instead, you'll probably use some code to call CraftQL dynamically. The easiest way to simulate this is in your Terminal with `curl`.

```bash
$ curl -H "authorization: bearer {token}" -H "Content-type: application/json" -d '{"query":"{ helloWorld }"}' http://localhost/api
```

Note: in order to make this work you have to create a Token in the CraftQL settings. Once generated you can pass the token through an `authorization` header, as shown above.

