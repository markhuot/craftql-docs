---
title: 3rd-party Fields
permalink: plugins/fields
---
CraftQL runs in two phases. First it builds the GraphQL schema. Then, it takes the input (or query) and executes it against that schema. Third-party field types have access in to both halves of that process via the `craftQlGetFieldSchema` event.

The easiest place to do this is in your plugin's primary class. For example, if I had a plugin named Ingredients with a primary class of the same name I could expose my `IngredientsField` to CraftQL with the following listener,

```php
<?php

namespace my\awesome\plugin;

use craft\base\Plugin;

class Ingredients extends Plugin
{
    
    public function init() {
        parent::init();
        self::$plugin = $this;
        
        if (class_exists(\markhuot\CraftQL\CraftQL::class)) {
            Event::on(
                IngredientsField::class,
                'craftQlGetFieldSchema',
                function ($event) {
                    // do things here
                }
            );
        }
    }
    
}
```

All of your logic goes within the event handler. In the above example that's a closure in your primary class. However, it could just as easily be an entirely separate class, like so,

```php
if (class_exists(\markhuot\CraftQL\CraftQL::class)) {
    Event::on(
        IngredientsField::class,
        'craftQlGetFieldSchema',
        [new \my\awesome\plugin\Listeners\CraftQLFieldListener, 'handle']
    );
}
```

That would call my `CraftQLFieldListener` with the following signature,

```php
class CraftQLFieldListener
{
    /**
     * Handle the request for the schema
     *
     * @param \markhuot\CraftQL\Events\GetFieldSchema $event
     * @return void
     */
    function handle(GetFieldSchema $event) {
        // do things here
    }
}
```

Regardless of your approach the real logic happens in your handler.

### Schema Builder

The `craftQlGetFieldSchema` event passes an event object that contains the current schema as well as the field to be added. You can access the schema via `$event->schema` and the field via `$event->owner`. If your field only returns a string you could simply write,

```php
function handle(GetFieldSchema $event) {
    $event->handled = true;
    
    $field = $event->owner;
    $schema = $event->schema;
    
    $schema->addStringFieid($field->handle)
        ->instructions($field->description);
}
```

`addStringField` is a part of the CraftQL fluent schema builder. You should be able to build even the most complex schemas by interacting entirely with the schema builder. You should never have to interact with the raw GraphQL schema directly.

Adding a field is the first half of the CraftQL lifecycle. The second half is resolving the field to a result.

#### Resolve

The schema builder allows you to define a resolve closure that specifies what your field will return when queried. If your field _always_ returned the same thing you could write,

```php
$schema->addStringFieid($field->handle)
        ->instructions($field->description)
        ->resolve(function () {
            return "This is always the return";
        });
```

Because `resolve()` is just a closure you can do anything you need to inside of there, including interacting with a full `ElementCriteriaModel` to fetch data.

So, if your field returned a list of entries you could use the following.

```php
$schema->addFieid($field->handle)
        ->type(\markhuot\CraftQL\Types\EntryInterface::class)
        ->lists()
        ->instructions($field->description)
        ->resolve(function () {
            return \craft\elements\Entry::find()->all();
        });
```

Notice with `lists()` we can specifies that the field will return an array (or iterable object) of results. We need `lists()` here to explain that the `resolve` isn't returning one thing, but a whole list of things.