# v.scss

`v.scss` brings a single SCSS function for shorter access (4 characters saved!) to [CSS custom properties](https://developer.mozilla.org/en-US/docs/Web/CSS/var) : `v(propName)` instead of `var(--propName)`. It also improves fallbacks chaining.

⚠️ While this helper remains cool, I have decided to not use it outside of personal projects. Compared to IDE auto-completion, its benefits might not be worth the cognitive load linked to any additional abstraction.

## Table of contents

- [Installation](#installation)
- [Usage](#usage)
  - [Fallback value as optional second parameter](#fallback-value-as-optional-second-parameter)
  - [Multiple fallbacks](#multiple-fallbacks)
- [Edge cases](#edge-cases)
  - [SCSS interpolation](#scss-interpolation)
  - [`--` is a valid custom property name](#---is-a-valid-custom-property-name)
- [Changelog](#changelog)
- [Seel also](#see-also)

## Installation

1. `npm install v.scss` pulls the package into your project.
2. `@import '~v.scss';` in a SCSS files makes `v()` available.

## Usage

Declare your CSS custom properties as you usually do:
```css
:root {
  --primary: #000;
  --bg: #fff;
}
```
Then, access them with `v()`.
```scss
html {
  background: v(bg);
  color: v(primary);
}
```
That’s it! Here’s the generated CSS:

```css
html {
  background: var(--bg);
  color: var(--primary);
}
```

### Fallback value as optional second parameter

The CSS `var()` function can take a fallback value as second parameter: if the wanted custom property isn’t defined or valid for the browser, the fallback will be used.

```scss
:root {
  --primary: cyan;
  --bg: #433221;
}

html {
  background: v(bg, brown); // `background: var(--bg, brown);`
  color: v(primaryyyy, yellow); // `color: var(--primaryyyy, yellow);`
}
```

The `background` will be `#433221` (`--bg` value) but the `color` will be `yellow` because `--primaryyyy` doesn’t exist.


### Multiple fallbacks

You can have multiple fallbacks by chaining multiple custom properties names. The last parameter is always a fallback value.

```css
html {
  color: v(primary, accent, bg, #f0f0f0);

  // generates
  color: var(--primary, var(--accent, var(--bg, #f0f0f0)));
}
```

Notes:
- if you need the last parameter to not be a fallback value, replace it by `null`;
- if you need [more parameters to not be fallback values](https://github.com/meduzen/v-helper/issues/8), wrap them in quotes `v()`, as described [in a comment](https://github.com/meduzen/v-helper/issues/8#issuecomment-1368505230).

## Edge cases

### SCSS interpolation

In order to assign a value to a custom property using a SCSS variable or a SCSS function, [interpolation](https://github.com/sass/sass/issues/2516) is required:
```scss
$primary: #000;

.my-class {
  --primary: $primary; // error 🚫, custom property assignment needs interpolation
  --primary: #{$primary}; // correct ✅, value interpolated with `#{}`
  --primary: #000; // correct ✅, regular syntax

  --accent: v(secondary); // error 🚫, custom property assignment needs interpolation
  --accent: #{v(secondary)}; // correct ✅, function interpolated
  --accent: var(--secondary); // correct ✅, regular syntax

  color: v(accent); // correct ✅, `color` is not a custom property
}
```

Interpolation means “Have a look at `#{what is inside the curly braced}` and replace the `$value-string` by its computed value (`$000`)”.

In situations where interpolation is needed, using `v()` is less readable (`#{v(propName)}`) than the standard syntax (`var(--propName)`).

### `--` is a valid custom property name

It turns out that [`--` is a valid name for a CSS custom property](https://twitter.com/alexzaworski/status/1127688935541338112).

Declaring and using it is all about edge cases:
```scss
.my-class {
  --: .5; // error 🚫
  --#{''}: .5; // correct ✅
  #{'--'}: .5; // correct ✅

  opacity: var(--); // error 🚫
  opacity: var(#{'--'}); // correct ✅
  opacity: v(); // correct ✅, thanks to v() ✌️
}
```

Another example, with three dashes:
```scss
.my-class-with-more-dashes {
  --#{'-'}: .5; // correct ✅
  #{'---'}: .5; // correct ✅

  opacity: var(#{'---'}); // correct ✅, interpolated
  opacity: v('-'); // correct ✅, thanks to v() ✌️
}
```

## Changelog

See [releases](https://github.com/meduzen/v-helper/releases).

## See also

- Custom properties [on Can I Use](https://caniuse.com/#feat=css-variables).
- [davidkpiano/sass-v](https://github.com/davidkpiano/sass-v), a similar project with more features, done way before mine 🤭.
- [malyw/css-vars](https://github.com/malyw/css-vars), a SCSS mixin allowing you to start writing some CSS custom properties even if the browsers you target don’t support them.
- [postcss-custom-properties](https://github.com/postcss/postcss-custom-properties), a PostCSS plugin with a similar purpose.
- [_Dark theme in a day_](https://medium.com/@mwichary/dark-theme-in-a-day-3518dde2955a), an all-round article with a lot of CSS custom properties.
