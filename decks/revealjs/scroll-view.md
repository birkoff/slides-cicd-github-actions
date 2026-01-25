## Scroll View
The scroll view is activated by initializing reveal.js with `view: "scroll"`. Here's a demo of it in action.

```js
Reveal.initialize({
  // Activate the scroll view
  view: 'scroll',

  // Force the scrollbar to remain visible
  scrollProgress: true,
});
```

---

## Scrollbar

By default, the scrollbar is automatically hidden when you stop scrolling. This behavior can be configured using `scrollProgress`.

```js
// - auto:     Show the scrollbar while scrolling, hide while idle
// - true:     Always show the scrollbar
// - false:    Never show the scrollbar
scrollProgress: 'auto';
```

