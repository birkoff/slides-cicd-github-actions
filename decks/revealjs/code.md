# Presenting Code
---

Code that will be syntax highlighted. When the `data-trim` attribute is present, surrounding whitespace within the `<code>` is automatically removed.


```html
<section>
  <pre><code data-trim data-noescape>
(def lazy-fib
  (concat
   [0 1]
   ((fn rfib [a b]
        (lazy-cons (+ a b) (rfib b (+ a b)))) 0 1)))
  </code></pre>
</section>
```
HTML will be escaped by default. To avoid this, add `data-noescape` to the `<code>` element.

---

## Theming

Make sure that a syntax highlight theme is included in your document. We include Monokai by default, which is distributed with the reveal.js repo at [plugin/highlight/monokai.css](https://github.com/hakimel/reveal.js/tree/master/plugin/highlight/monokai.css). A full list of available themes can be found at <https://highlightjs.org/demo/>.

```html
<link rel="stylesheet" href="plugin/highlight/monokai.css" />
<script src="plugin/highlight/highlight.js"></script>
<script>
  Reveal.initialize({
    plugins: [RevealHighlight],
  });
</script>
```
---
## Line Numbers & Highlights

You can enable line numbers by adding `data-line-numbers` to your `<code>` tags. If you want to highlight specific lines you can provide a comma separated list of line numbers using the same attribute. For example, in the following example lines 3 and 8-10 are highlighted:

```html
<pre><code data-line-numbers="3,8-10">
<table>
  ...
</table>
</code></pre>
```

---

#### Line Number Offset <span class="r-version-badge new">4.2.0</span>

You can offset the line number if you want to showcase a excerpt of a longer set of code. In the example below, we set `data-ln-start-from="7"` to make the line numbers start from 7.

```html
<pre><code data-line-numbers data-ln-start-from="7">
<tr>
  <td>Oranges</td>
...
</tr>
</code></pre>
```

---

## Step-by-step Highlights

You can step through multiple code highlights on the same code block. Delimit each of your highlight steps with the `|` character. For example `data-line-numbers="1|2-3|4,6-10"` will produce three steps. It will start by highlighting line 1, next step is lines 2-3, and finally line 4 and 6 through 10.

```html
<pre><code data-line-numbers="3-5|8-10|13-15">
<table>
  <tr>
...
</table>
</code></pre>
```
---

## Language selection

By default, [highlight.js](https://highlightjs.org/) tries to automatically detect which language is used in the code snippet. It is however possible to overwrite this by adding a `language-XXX` attribute. The full list of supported languages is available on [their documentation](https://highlightjs.readthedocs.io/en/latest/supported-languages.html).

```html
<pre><code data-trim class="language-python">
>>> import antigravity
>>> print(b"\x01\x02\x03")
>>> a = 2
</code></pre>
```

--- 
## Manual Highlighting

All of your code blocks are automatically syntax highlighted when reveal.js starts. If you want to disable this behavior and trigger highlighting on your own you can set the `highlightOnLoad` flag to false.

```js
Reveal.initialize({
  highlight: {
    highlightOnLoad: false,
  },
  plugins: [RevealHighlight],
}).then(() => {
  const highlight = Reveal.getPlugin('highlight');
  highlight.highlightBlock(/* code block element to highlight */);
});
```s