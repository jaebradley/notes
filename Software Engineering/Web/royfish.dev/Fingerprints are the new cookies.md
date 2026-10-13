# [Fingerprints are the new cookies](https://royfish.dev/fingerprints-are-the-new-cookies)
* HTTP requests that originate from a browser contain metadata about the client like the language (`Accept-Language` header), operating system (`User-Agent` header), IP address
* Farbling is when noise is added to the metadata to make the fingerprint inconsistent
* Another approach to combat fingerprinting is to make everything look the same so there's no telling any client users apart
* When one can execute code on a device, there are ways to measure the device's hardware qualities like GPU, CPU, memory size, device model, installed fonts, etc
* There are millions of devices that use the latest Chrome, and there are millions of Macs with an M4 chip, and there are millions of browsers that are in French
  * But there are relatively very few devices that run the latest Chrome in French on an M4 Mac
* The fingerprint doesn't need to be globally unique, just unique enough to tell people apart in some sample size
* ZIP code (inferred from IP address) + birth date + sex is enough to uniquely identify 60%+ of Americans

## Information Entropy
* The bits from an answer are called its surprisal and a rarer answer contains more information
  * Example: 20 questions-like game
  * If 50% of all characters are men and 50% of all characters are women, asking if a character is a man/woman contains `1` bit of information because it cut the candidate pool in half
  * `log base 2 (100/50) = log base 2 (2) = 1` bit of information
  * In other words, a trait shared by `1` in `2` people can be represented via `1` bit of information, a trait shared by `1` in `8` can be represented by `3` bits of information, `1` in `n` can be represented by `log base 2 (n)` bits

## methods used on this page
* `OfflineAudioContext` can render a short tone without playing it, and tiny float differences in the result depend on browser's audio engine and CPU
* `WebGL` reads GPU vendor and model

```javascript
const gl = document.createElement('canvas').getContext('webgl');
const dbg = gl.getExtension('WEBGL_debug_renderer_info');
return gpuName(gl.getParameter(dbg ? dbg.UNMASKED_RENDERER_WEBGL : gl.RENDERER) || '');
// "ANGLE (Apple, ANGLE Metal Renderer: Apple M4, Unspecified Version)" -> "apple m4"
```

* `canvas` can draw text and the font rendering depends on OS, GPU, and fonts
* `Intl` API can be used to identify timezone / date / number settings
  * VPN changes IP address but not time zone
* `navigator` API can be used to identify number of CPU cores, memory size and browser language
