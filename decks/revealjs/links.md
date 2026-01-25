# Internal links

---

Give your target slide a unique `id` attribute. Next, you can create an anchor with an href `#/<id>`.

```html/1,8
<section>
	<a href="#/grand-finale">Go to the last slide</a>
</section>
<section>
	<h2>Slide 2</h2>
</section>
<section id="grand-finale">
	<h2>The end</h2>
	<a href="#/0">Back to the first</a>
</section>
```

---

## Numbered Links

It's also possible to link to slides based on their slide index.

 ```html
<a href="#/2">Go to 2nd slide</a>
<a href="#/3/2">Go to the 2nd vertical slide inside of the 3rd slide</a>
```

---

## Navigation Links

You can add relative navigation links that work similarly to the built in directional control arrows. 

```html
<button class="navigate-left">Left</button>
<button class="navigate-right">Right</button>
<button class="navigate-up">Up</button>
<button class="navigate-down">Down</button>

<!-- Previous vertical OR horizontal slide -->
<button class="navigate-prev">Prev</button>

<!-- Next vertical OR horizontal slide -->
<button class="navigate-next">Next</button>
```
