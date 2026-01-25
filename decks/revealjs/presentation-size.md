## <> Presentation Size <>

```javascript
Reveal.initialize({
  // The "normal" size of the presentation, aspect ratio will
  // be preserved when the presentation is scaled to fit different
  // resolutions. Can be specified using percentage units.
  width: 960,
  height: 700,

  // Factor of the display size that should remain empty around
  // the content
  margin: 0.04,

  // Bounds for smallest/largest possible scale to apply to content
  minScale: 0.2,
  maxScale: 2.0,
});
```
---
## Center

Slides are vertically centered on the screen based on how much content they contain. To disable this and leave slides fixed at their configured height set `center` to `false`.

```js
Reveal.initialize({ center: false });
```