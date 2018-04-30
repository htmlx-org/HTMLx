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
  <button disabled >can't touch this</button>
</div>
```

As in HTML, values may be unquoted:

```html
<input type=checkbox >
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


### Text expressions

Text can also contain JavaScript expressions:

```html
<h1>Hello {name}!</h1>
<p>{a} + {b} = {a + b}.</p>
```


### Control flow

TODO

### Directives

TODO

### script/style

TODO

### Inline HTML

TODO