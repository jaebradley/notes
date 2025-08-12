# [It’s time for modern CSS to kill the SPA](https://www.jonoalderson.com/conjecture/its-time-for-modern-css-to-kill-the-spa/)
* Issues with SPAs
  * Delayed navigation while scripts rehydrate components
  * Layout shift, content popping, full-page skeleton
  * Performance hit that's disproportionate to the effect
* M/KBs of JS to fake native navigation
* Chromium-based browsers support native, declarative page transitions via the View Transitions API
  * Fade between pages
  * Animate shared elements
  * Maintain persistent elements like headers or navbars

## Basic cross-page fade transition
```css
@view-transition {
  navigation: auto;
}

::view-transition-old(root),
::view-transition-new(root) {
  animation: fade 0.3s ease both;
}

@keyframes fade {
  from { opacity: 0; }
  to   { opacity: 1; }
}
```

## Shared element transitions
```html
<a href="/product/red-shoes">
  <img src="/images/red-shoes-thumb.jpg" style="view-transition-name: product-image;" />
</a>

<img src="/images/red-shoes-large.jpg" style="view-transition-name: product-image;" />
```

## Manually trigger transitions inside a page with
```javascript
document.startViewTransition(() => {
  document.body.classList.toggle('dark-mode');
});
```

## Speculation Rules
```javascript
<script type="speculationrules">
{
  "prerender": [
    {
      "where": {
        "selector_matches": "a"
      }
    }
  ]
}
</script>
```

* On a lean site, they make things feel instant
* But if pages are slow, bloated, JavaScript-heavy, speculation front-loads costs
