## Backgrounds
#### Four different types of backgrounds are supported: color, image, video and iframe.

You can apply full page backgrounds outside of the slide area by adding a `data-background` attribute to your `<section>` elements. 

---

## Color Backgrounds

All CSS color formats are supported, including hex values, keywords, `rgba()` or `hsl()`.

```html/0,3
<section data-background-color="aquamarine">
  <h2>🍦</h2>
</section>
<section data-background-color="rgb(70, 70, 255)">
  <h2>🍰</h2>
</section>
```

---

<div class="reveal reveal-example">
  <div class="slides">
    <section data-background-color="aquamarine">
      <h2 style="font-size: 4em;">🍦</h2>
    </section>
    <section data-background-color="rgb(70, 70, 255)">
      <h2 style="font-size: 4em;">🍰</h2>
    </section>
  </div>
</div>

---


## Image Backgrounds

By default, background images are resized to cover the full page. Available options:

| Attribute                | Default <div style="width:80px"></div> | Description                                                                                       |
| :----------------------- | :------------------------------------- | :------------------------------------------------------------------------------------------------ |
| data-background-image    |                                        | URL of the image to show. GIFs restart when the slide opens.                                      |
| data-background-size     | cover                                  | See [background-size](https://developer.mozilla.org/docs/Web/CSS/background-size) on MDN.         |
| data-background-position | center                                 | See [background-position](https://developer.mozilla.org/docs/Web/CSS/background-position) on MDN. |
| data-background-repeat   | no-repeat                              | See [background-repeat](https://developer.mozilla.org/docs/Web/CSS/background-repeat) on MDN.     |
| data-background-opacity  | 1                                      | Opacity of the background image on a 0-1 scale. 0 is transparent and 1 is fully opaque.           |

{.nowrap-1st}

```html/0,3-4
<section data-background-image="http://example.com/image.png">
  <h2>Image</h2>
</section>
<section data-background-image="http://example.com/image.png"
          data-background-size="100px" data-background-repeat="repeat">
  <h2>This background image will be sized to 100px and repeated</h2>
</section>
```

## Video Backgrounds

Automatically plays a full size video behind the slide.

| Attribute                   | Default | Description                                                                             |
| :-------------------------- | :------ | :-------------------------------------------------------------------------------------- |
| data-background-video       |         | A single video source, or a comma separated list of video sources.                      |
| data-background-video-loop  | false   | Flags if the video should play repeatedly.                                              |
| data-background-video-muted | false   | Flags if the audio should be muted.                                                     |
| data-background-size        | cover   | Use `cover` for full screen and some cropping or `contain` for letterboxing.            |
| data-background-opacity     | 1       | Opacity of the background video on a 0-1 scale. 0 is transparent and 1 is fully opaque. |


---


```html/0-1
<section data-background-video="https://static.slid.es/site/homepage/v1/homepage-video-editor.mp4"
          data-background-video-loop data-background-video-muted>
  <h2>Video</h2>
</section>
```
