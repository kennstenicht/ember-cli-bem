# ember-cli-bem [![Build Status](https://travis-ci.org/nikityy/ember-cli-bem.svg?branch=master)](https://travis-ci.org/nikityy/ember-cli-bem) [![Ember Observer Score](https://emberobserver.com/badges/ember-cli-bem.svg)](https://emberobserver.com/addons/ember-cli-bem)

ember-cli-bem helps you define class names in BEM way. Make your templates shorter with a help of [`elem`](https://github.com/nikityy/ember-cli-bem/blob/master/addon/helpers/elem.js) helper. Write less code in your components by injecting [BEM mixin](https://github.com/nikityy/ember-cli-bem/blob/master/addon/mixins/bem.js). ember-cli-bem encapsulates all BEM naming logic so you don't have to reinvent the wheel.

## Installation

```sh
ember install ember-cli-bem
```

## Introduction

Why use another class names addon in your Ember project, if you're already
using something like [`ember-component-css`](https://github.com/ebryn/ember-component-css)? Because BEM-way
classes look ugly in these addons.

For example, compare how to create an element with [`ember-component-css`](https://github.com/ebryn/ember-component-css) and with `ember-cli-bem`:
```hbs
  {{!-- With ember-component-css --}}
  <div class="{{componentCssClassName}}__container"></div>

  {{!-- With ember-cli-bem --}}
  <div class="{{elem 'container'}}"></div>
```

Moreover, how to define an element with modifiers? If you want to get following class names `header__item header__item_disabled header__item_type_highlighted`, plus if `disabled` modifier
applies only if some condition matches, you have to write something like this:

```hbs
  {{!-- With ember-component-css --}}
  <div class="
    {{componentCssClassName}}__item
    {{if someCondition (concat componentCssClassName '__item_disabled')}}
    {{componentCssClassName}}__item_type_highlighted
  "></div>

  {{!-- With ember-cli-bem --}}
  <div class="{{elem 'item' disabled=someCondition highlighted=true}}"></div>
```

Or let's say you just want to define some modifier in your component:

```js
// With ember-component-css
import Ember from 'ember';
export default Ember.Component.extend({
  classNameBindings: ['typeMod'],
  type: 'highlighted',
  typeMod: Ember.computed('componentCssClassName', 'type', function() {
    const type = this.get('type');
    if (type) {
      return `${this.get('componentCssClassName')}_type_${type}`
    } else {
      return '';
    }
  }),
});

// With ember-cli-bem
import Ember from 'ember';
import BEM from 'ember-cli-bem/mixins/bem';

export default Ember.Component.extend({
  blockName: 'button',
  mods: ['type'],
  type: 'highlighted',
});
```

Once again, `ember-cli-bem` encapsulates all BEM naming logic, so you don't have to care
about it in your components.

## Defining Blocks and Elements

To define a [block](https://en.bem.info/methodology/key-concepts/#block) with `ember-cli-bem`, you should mix BEM mixin into your component. Blocks in `ember-cli-bem` requires only one field — `blockName`. If you left this property empty, component will throw an error. `blockName` just concatenates with `classNames` property.

If you want to define an [element](https://en.bem.info/methodology/key-concepts/#element) component (with `block__element` class name and custom logic), you just add
`elemName` property in component definition:

```js
import Ember from 'ember';
import BEM from 'ember-cli-bem/mixins/bem';

export default Ember.Component.extend(BEM, {
  blockName: 'button',
  elemName: 'icon'
});
```

This component will have a `button__icon` class.

## Defining Modifiers

When you want to define a [modifier](https://en.bem.info/methodology/key-concepts/#modifier) for your component, you should define `mods` array, which acts just like `classNameBindings`, but with some BEM attached:

```js
import Ember from 'ember';
import BEM from 'ember-cli-bem/mixins/bem';

export default Ember.Component.extend(BEM, {
  blockName: 'button',

  mods: [
    'disabled',
    'type',
    'clear:with-clear:without-clear'
  ],

  disabled: true,
  type: 'submit',
  clear: false,
});
```

This component will have a `button` class with following modifier classes:
* `button_disabled`
* `button_type_submit`
* `button_without-clear`

## Using elem helper

In templates of your BEM-components you could use `elem` helper to generate BEM class names:

```hbs
  <div class="{{elem 'icon'}}"></div>
  <div class="{{elem 'caption'}}">
    {{text}}
  </div>
```

Applying `button` example, these elements will have `button__icon` and `button__caption` classes accordingly.

Also `elem` helper supports modifiers:
```hbs
  <div class="{{elem 'container' collapsed=true type='float'}}">
    {{yield}}
  </div>
```

So `container` element will have following class names:
* `button__container`
* `button__container_collapsed`
* `button__container_type_float`

## Configuring Addon

BEM-naming can be configured. You can change element and mod delimiters, to use or not
key-value modifiers, or define your own naming strategy.

```js
ENV['ember-cli-bem'] = {
  elemDelimiter: '__',
  modDelimiter: '_',
  useKeyValuedMods: true,
}
```

### elemDelimiter
Default value: `'__'`

Defines how to separate block and element name. For block `checkbox`, element `input`, and elemDelimiter `-`
it will produce `checkbox-input` class name.

### modDelimiter
Default value: `'_'`

Just like elemDelimiter, it defines how to separate modifier class name from the rest of name. For block 'checkbox', modifier `disabled`, and modDelimiter `--`
it will produce `checkbox--disabled` classname

### useKeyValuedMods
Default value: `true`

If false, addon will not create key-value modifiers. Refer to the following
table to understand what class will be generated with this option enabled or disabled:

| Modifier Value | With `useKeyValuedMods`  | Without `useKeyValuedMods` |
|----------------|--------------------------|----------------------------|
| `true`         | `'block_disabled'`       | `'block_disabled'`         |
| `false`        | `''`                     | `''`                       |
| `''`           | `''`                     | `''`                       |
| `'force'`      | `'block_disabled_force'` | `'block_disabled'`         |

Note the case with `'force'` modifier value.
