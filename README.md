# v.scss

`v.scss` brings a unique SCSS function that allows easier access to [CSS custom properties](https://developer.mozilla.org/en-US/docs/Web/CSS/var) using `v(propName)` instead of `var(--propName)`.

Yup, `v` purpose is better readability by saving you 4 characters. It’s all it does.

## Installation

1. `npm install v.scss` pulls the package into your project.
2. `@import '~v.scss';` in a SCSS files make `v()` available.

## Usage

Start by defining some CSS custom properties:
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

The CSS `var()` function can take a fallback as second parameter: if the wanted custom property isn’t defined or valid for the browser, this default value will be used.

In the following example, the page background color will be `#433221`, the text will receive the `yellow` color and links the `cyan` one.
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

## See also

- Custom properties [on Can I Use](https://caniuse.com/#feat=css-variables).
- [malyw/css-vars](https://github.com/malyw/css-vars), a SCSS mixin allowing you to start writing some CSS custom properties even if the browsers you target don’t support them.
- [postcss-custom-properties](https://github.com/postcss/postcss-custom-properties), a PostCSS plugin with a similar purpose.
- [_Dark theme in a day_](https://medium.com/@mwichary/dark-theme-in-a-day-3518dde2955a), an all-round article with a lot of CSS custom properties.
