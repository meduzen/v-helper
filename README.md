
# v.scss

`v.scss` brings a unique SCSS function that allows easier access to [CSS custom properties](https://developer.mozilla.org/en-US/docs/Web/CSS/var) using `v(propName)` instead of `var(--propName)`.

Yup, `v` purpose is better readability by saving you 4 characters. It’s all it does.

## Installation

1. `npm install v.scss` pulls the package into your project.
2. `@import '~v.scss';` in a SCSS files make `v()` available.

## Usage

Start by declaring some CSS custom properties:
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
That’s it! This will generates regular CSS:

```css
html {
  background: var(--bg);
  color: var(--primary);
}
```

### Fallback value as optional second parameter

The CSS `var()` function can take a fallback as second parameter: if the wanted custom property isn’t defined or valid for the browser, this parameter will be used.

In the following example, the background color will be `#433221`, the text will receive the `yellow` color and links the `cyan` one.
```scss
:root {
  --primary: cyan;
  --bg: #433221;
}

html {
  background: v(bg, brown); // `background: var(--bg, brown);`
  color: v(primaryyyy, yellow); // `color: var(--primary, yellow);`
}

a {
  color: v(primary); // color: var(--primary);
}
```
View it on [CodePen](https://codepen.io/meduzen/pen/YRyEPe).

## Edge cases

### SCSS interpolation

In the first example of this documentation, custom properties are assigned their final values:
```scss
:root {
  --primary: #000; // `#000` stays the same after compilation, it’s the final value
}
```

In order to use SCSS variables instead of final values, interpolation is required:
```scss
// all-my-variables.scss
$primary-color: #000;

// my-global-layout.scss
:root {
  --primary: $primary-color; // error 🚫, custom property assignment needs interpolation
  --primary: #{$primary-color}; // correct ✅, value interpolated with `#{}`
}
```
Interpolation means “Have a look at `#{what is inside the curly braced}` and replace the `$value-string` by its computed value (`$000`)”. In other words, `--primary: #{$primary-color}` is compiled to `--primary: #000`, as expected.

Assigning the computed value of a SCSS function follows the same [interpolation rules](https://github.com/sass/sass/issues/2516). `v()` being a function, using it to assign the value of a CSS custom property to _another_ custom property requires interpolation:
```scss
.my-class-that-erases-the-root-color {
  --superColor: var(--secondaryColor); // correct ✅, regular syntax
  --superColor: v(secondaryColor); // error 🚫, custom property assignment needs interpolation
  --superColor: #{v(secondaryColor)}; // correct ✅, function interpolated with `#{}`
  
  color: v(superColor); // correct ✅, `color` is not a custom property
}
```

In that case, as `v()`’s purpose is to increase readability and bring some coolness ✌️ in the assignment of CSS custom properties, one may favor sticking to the standard syntax (`var(--propName)`) instead of insulting `v()` by putting it in an even more uncool syntax (`#{v(propName)}`).

### `--` is a valid custom property name

It turns out that [`--` is a valid name for a CSS custom property](https://twitter.com/alexzaworski/status/1127688935541338112).

Declaring and using it is all about edge cases:
```scss
.my-class-with-weird-declarations {
  --: .5; // error 🚫, expected "}", was "--: 0.5;"
  --#{''}: .5; // correct ✅
  #{'--'}: .5; // also correct ✅

  opacity: var(--); // error 🚫, Invalid CSS after "var(--": expected expression (e.g. 1px, bold), was ");"
  opacity: var(#{'--'}); // correct ✅
  opacity: v(); // correct ✅, thanks to v() coolness ✌️
}
```

Other example with three dashes instead of two dashes:
```scss
.my-class-with-more-dashes {
  --#{'-'}: .5; // correct ✅
  #{'---'}: .5; // also correct ✅
  opacity: var(#{'---'}); // correct ✅, interpolated
  opacity: v('-'); // correct ✅, thanks to v() coolness ✌️
}
```

## See also

- Custom properties [on Can I Use](https://caniuse.com/#feat=css-variables).
- [davidkpiano/sass-v](https://github.com/davidkpiano/sass-v), a similar project with more features, done way before mine 🤭.
- [malyw/css-vars](https://github.com/malyw/css-vars), a SCSS mixin allowing you to start writing some CSS custom properties even if the browsers you target don’t support them.
- [postcss-custom-properties](https://github.com/postcss/postcss-custom-properties), a PostCSS plugin with a similar purpose.
- [_Dark theme in a day_](https://medium.com/@mwichary/dark-theme-in-a-day-3518dde2955a), an all-round article with a lot of CSS custom properties.
