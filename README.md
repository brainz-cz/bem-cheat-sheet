# BEM Cheat Sheet
BEM (Block, Element, Modifier) is a component-based approach to web development. The idea behind it is to divide the user interface into independent blocks. This makes interface development easy and fast even with a complex UI, and it allows reuse of existing code without copying and pasting.

## Block
A functionally independent page component that can be reused. It is meaningful on its own.

- Block name describes its purpose (`menu`, `button`), not its state (`red` or `big`)
- Never use CSS tag (`a`, `span`) or ID selectors (`#post`)
- A block should not influence its environment, e.g. by setting margins or absolute positioning. This should be done by the parent.

```html
<!-- CORRECT -->
<div class="error"></div>

<!-- INCORRECT -->
<div class="red-text"></div>
```

## Element
A part of a block that cannot be used separately from it.

- The element name describes its purpose and is separated from the block name with a double underscore (`__`)

```html
<form class="search-form">
  <input class="search-form__input" />
  <input class="search-form__input" />
  <button class="search-form__button"></button>
</form>
```

## Modifier
An entity that defines the appearance, state, or behavior of a block or element.

- The modifier name is separated from the block or element by a double dash (`--`)
- Can define appearance (`button--red`) or state (`button--disabled`)
- There are two types of modifiers. Boolean (`button--large`) and key-value pairs (`button--text-red`)
- For easier manipulation in JavaScript, we can also use helper modifier classes such as `is-active`, `is-focused` etc.

```scss
.button {

  &--large {}

  &--text-red {}
}

.menu {

  &__item {

    &.is-active {}
  }
}
```

## Block Nesting
- Blocks can be nested in each other
- You can have as many nesting levels as you want
- An element is always part of a block (`post__header`), not another element (`post__header__author`)
- While the CSS structure is flat, the DOM structure does not need to be. (See `post__content` and `post__image` in the following example.)

```html
<article class="post">
  <div class="post__header">
    <div class="post-header">
      <div class="post-header__author"></div>
      <div class="post-header__date"></div>
    </div>
  </div>
  <div class="post__content">
    <img class="post__image" />
  </div>
</article>
```

While block nesting is allowed in BEM (and absolutely necessary for most websites), blocks should never visually influence one another.

```scss
// INCORRECT
.page {

  &--home {

    .post {}
  }
}
```

Additionally, we should avoid targeting HTML tags and IDs whenever possible.

```scss
// INCORRECT
.post {

  p {}

  img {}
}
```

In some cases however, we might have no other choice. One such example is the base styling of links or other elements throughout the website. The same applies for styling content received from WYSIWYG editors.

```scss
// OKAY
a {
  color: currentColor;
}

.content-from-admin {
  p {
    margin-bottom: 1em;
  }
}
```

## SCSS Structure
To keep your SCSS file organized, you should have one block per file, with elements and modifiers nested.

```scss
.post {
  border: 1px solid $black;

  &--featured {
    border-width: 2px;
  }

  &__image {
    width: 50%;

    &--large {
      width: 100%;
    }
  }
}
```

The same applies for mixin includes.

```scss
.post {
  font-size: 14px;

  @include mq($md) {
    font-size: 16px;
  }

  &--featured {
    font-size: 16px;

    @include mq($md) {
      font-size: 18px;
    }
  }
}
```

## Breaking BEM Rules

Sometimes, you don't need to strictly stick to the BEM methodology. One such example are block modifiers which should also modify the appearance of child elements.

Consider an unordered list where some of the items should be highlighted. These should include a different colored text and larger images. In an ideal BEM world, this is how we would achieve this:

```html
<ul class="list">
  <li class="list__item">
    <div class="list__text">Lorem ipsum</div>
    <img src="img.jpg" class="list__image" />
  </li>
  <li class="list__item">
    <div class="list__text list__text--featured">Lorem ipsum</div>
    <img src="img.jpg" class="list__image list__image--large" />
  </li>
</ul>
```

```scss
.list {

  &__item {}

  &__text {
    color: $black;

    &--highlighted {
      color: $red;
    }
  }

  &__image {
    width: 100px;
    height: 100px;

    &--large {
      width: 200px;
      height: 200px;
    }
  }
}
```

However, this means we need to assign modifiers to two different elements. In this case, we might choose to break BEM rules and use a modifier for the whole block instead. (We need to save the block as a variable, in this case `$s` for **s**elector.)

```html
<ul class="list">
  <li class="list__item">
    <div class="list__text">Lorem ipsum</div>
    <img src="img.jpg" class="list__image" />
  </li>
  <li class="list__item list__item--highlighted">
    <div class="list__text">Lorem ipsum</div>
    <img src="img.jpg" class="list__image" />
  </li>
</ul>
```

```scss
.list {
  $s: &;

  &__item {}

  &__text {
    color: $black;
  }

  &__image {
    width: 100px;
    height: 100px;
  }

  &--highlighted {

    #{$s}__text {
      color: $red;
    }

    #{$s}__image {
      width: 200px;
      height: 200px;
    }
  }
}
```
