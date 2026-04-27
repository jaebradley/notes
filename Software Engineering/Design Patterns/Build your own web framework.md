# [Build your own web framework](https://vercel.com/blog/build-your-own-web-framework)

* In the example, need to support a static landing page, and a semi-static, semi-dynamic product page

## Landing Page

* Can reduce image size without sacrificing quality by using latest image formats like `.webp` and `.avif`
* Since the landing page is static HTML, use a CDN to cache the static HTML file at each region / edge

## Products Page

* Defer loading of non-critical images for a faster first paint
* Displayed products should update after a certain amount of time / when new products are added

## Static Rendering

* Page's HTML is generated during build
* Transpilation step to convert React-based pages into static HTML
  * `ReactDOM`'s `renderToString` method
* React components aren't entirely static and contain some JavaScript (like for event handling)
  * So need to create JavaScript bundles to hydrate the static markup once it's been rendered
* Export a string literal for each component that the bundler uses to create a custom JS bundle for each bage
  * Bundler generates HTML and automatically injects the custom bundle as a deferred script

```javascript
export async function createStaticFile(Component,filePath) {
  const pageName = getPageName(filePath);
  const outdir = join(".vercel", "output", "static");
  await fs.ensureDir(outdir);
 
  await generateClientBundle({ filePath, outdir, pageName });
  
  return fs.writeFileSync(
    path.join(outdir, `${pageName}.html`),
    `<!DOCTYPE html>
      ...
      <body>
        <div id="root">${ReactDOMServer.renderToString(React.createElement(Component) )}</div>
        <script src="${pageName}.bundle.js" defer></script>
      </body>`
  );
}
```

* Takes `Component` that represents the page and the path where the component file is located
  * Create a bundle that will get injected into the HTML file

## Incremental Static Regeneration

* Pattern for pages that contain data that frequently updates by invalidating the cache and regenerating the page after a specific interval or based on an event
* Need a serverless function (`products.func/index.js`) containing the handler that takes care of the page's regeneration
* Need an optional fallback HTML that gets served when there's no cached version of the page available yet and the page is being generated in the background

### Serverless Functions

* Function runs in its own environment, meaning that the handler function has access to all the necessary code to generate HTML
* To include all the necessary code and dependencies, create a `node_modules` function within the function folder or bundle everything together into a single handler file
* Need two methods
  * a method to generate a client-side bundle used to hydrate the static HTML returned from the server
  * a method to bundle the necessary files into a script that's executed in the Lambda's handler
    * Responsible for server-rendering the HTML based on the page's exported component
    * Very similar to the static rendering form before, but generating HTML at request time and not build time

```javascript
export async function generateLambdaBundle(Component, funcFolder, pageName,outfile) {
  const html = ReactDOMServer.renderToString(React.createElement(Component)); 
  const { code: contents } = await transform(getHandlerCode(html, pageName));

  return await build({
    ...
    stdin: { contents, resolveDir: path.join(".") },
    outfile,
  });
};

const getHandlerCode = (html: string, pageName: string) => `
  export default (req, res) => {  
    res.setHeader('Content-type', 'text/html');
    res.end(\`<!DOCTYPE html>
    <html lang="en">
      ...
      <body>
        <div id="root">${html}</div>
        <script src="${pageName}.bundle.js" defer></script>
      </body>
    </html>\`)
  }
`;
```

### Serverless Functions

* Serverless functions can be used to server-render pages
* To server-render pages, need to create a function that generates the page's HTML on every request ("Lambda handler")
* This use-case is essentially a subset of ISR that creates a lambda bundle and a client-side bundle
* With ISR, the lambda gets invoked only when a user requests a page that has been cached longer than the revalidate value
* With SSR, the lambda function is invoked on every request
  * There is no automatic caching, resulting in unique responses every time
