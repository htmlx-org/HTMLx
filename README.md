# HTMLx

<em>One Template to rule them all,<br>
One Template to find them;<br>
One Template to bring them all<br>
and in the darkness bind() them</em>

— [Jason Miller](https://twitter.com/_developit/status/990943081066217472)

## Background

UI frameworks built around JSX — React, Preact, Inferno, Nerv and many others — have benefited from a shared set of tools for syntax highlighting, autocomplete, linting and so on.

Other frameworks, which use a template syntax built atop HTML — Svelte, Vue, Ractive, Glimmer etc — have historically been fragmented, meaning those tools need to be reinvented many times.

This (**work-in-progress**) document proposes a common language designed to meet the needs of all those frameworks. It is smaller in scope than the [unity component specification](https://github.com/TheLarkInn/unity-component-specification), as it is only concerned with template syntax and has no opinions about behaviour.

## Motivation and goals

A shared language would allow different frameworks to collaborate on tooling. It would also reduce friction for new users, who would no longer have to learn a new syntax for each new framework.

One key advantage of 'HTML-plus' languages is that you don't actually *need* tooling in order to be productive — most editors give you out-of-the-box support for things like syntax highlighting (though imperfect, as JavaScript expressions are treated as strings) and auto-closing tags. Tools like Emmet work with no additional setup. HTMLx should retain that benefit.

## Syntax

### Tags

A lowercase tag, like `<div>`, denotes a regular HTML element. A capitalised tag, such as `<Widget>`, indicates a *component*.

A framework might define its own special elements — these are namespaced with the framework's name. For example, Svelte provides a `<svelte:window>` element for declaratively adding event listeners to `window`.

### Attributes

By default, attributes work exactly like their HTML counterparts:

```html
<div class="foo">
  <button disabled>can't touch this</button>
</div>
```

As in HTML, values may be unquoted:

```html
<input type=checkbox>
```

Attribute values can contain JavaScript expressions:

```html
<a href="page/{p}">page {p}</a>
```

Or they can *be* JavaScript expressions:

```html
<button disabled={!clickable}>...</button>
```

An expression might include characters that would cause syntax highlighting to fail in regular HTML, in which case quoting the value is permitted. The quotes do not affect how the value is parsed:

```html
<button disabled="{number !== 42}">...</button>
```

It's often necessary to pass a property to an element or component directly, so a shorthand is permitted — these two are equivalent:

```html
<button disabled={disabled}>...</button>
<button {disabled}>...</button>
```

*Spread attributes* allow many attributes or properties to be passed to an element or component at once:

```html
<Widget {...things}/>
```

An element or component can have multiple spread attributes, interspersed with regular ones.


### Text expressions

Text can also contain JavaScript expressions:

```html
<h1>Hello {name}!</h1>
<p>{a} + {b} = {a + b}.</p>
```

### HTML expressions

In a text expression, characters like `<` and `>` are escaped. An expression can inject HTML with `{@html expression}`:

```html
<div class="blog-post">
  <h1>{post.title}</h1>
  {@html post.content}
</div>
```

Whether or not `post.content` is sanitized is up to the framework, rather than a concern of HTMLx.


### Control flow

Content that is conditionally rendered can be wrapped in an `{#if condition}` block, where `condition` is any valid JavaScript expression:

```html
{#if answer === 42}
  <p>what was the question?</p>
{/if}
```

Additional conditions can be added with `{:elseif condition}`, optionally ending in an `{:else}` clause:

```html
{#if porridge.temperature > 100}
  <p>too hot!</p>
{:elseif 80 > porridge.temperature}
  <p>too cold!</p>
{:else}
  <p>just right!</p>
{/if}
```

Iterating over lists of values can be done with an `{#each list as item}` block, where `list` is any valid JavaScript expression and `item` is a valid JavaScript identifier, or a destructuring pattern.

```html
<h1>Cats of YouTube</h1>
<ul>
  {#each cats as cat}
    <li><a target="_blank" href={cat.video}>{cat.name}</a></li>
  {/each}
</ul>
```

An `#each` block can also specify an *index*, equivalent to the second argument in an `array.map(...)` callback:

```html
{#each items as item, i}
  <p>{i}: {item.name}</p>
{/each}
```

It can also specify a *key expression* in parentheses — again, any valid JavaScript expression — which is typically used for list diffing when items are added or removed from the middle of the list:

```html
{#each items as item (item.id)}
  <p>{item.name}</p>
{/each}
```

An `#each` block can also have an `{:else}` clause, which is rendered if the list is empty:

```html
{#each shoppingCart as item}
  <p>{item.name}</p>
{:else}
  <p>Your shopping cart is empty!</p>
{/each}
```

*TODO: Svelte also has `await` blocks — does this section need more flexibility?*


### Directives

A framework may support *directives* on elements and components for declaratively adding event listeners, transitions and so on. The general form is `type:name={value}` — the `:` character distinguishes directives from attributes.

For example, a `click` event listener could be added with an `on:click` directive:

```html
<button on:click={handleClick}>click me!</button>
```

As with attributes, the value can be quoted, and the quotes will not affect how the directive value is parsed:

```html
<input on:change="{e => alert(`the value is ${e.target.value}`)}">
```

### script/style

An HTMLx string could include `<script>` and `<style>` tags. The contents of these tags must *not* be interpolated, but must instead be preserved as typed.

What happens to the contents of those tags is up to the framework.