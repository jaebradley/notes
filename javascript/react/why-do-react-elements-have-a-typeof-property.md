# [Why Do React Elements Have A `$$typeof` Property](https://overreacted.io/why-do-react-elements-have-typeof-property/)

* HTML was constructed via `someEl.innerHTML = '<p> + someMessage.text + '</p>'`
  * This works except when text is injected javascript / XSS attack
* React escapes values automatically
* Only way to render arbitrary HTML is to use `dangerouslySetHTML`
* React elements are represented as Javascript objects `{ type: 'div', props: { someProp: 'someProp value' } }`
* However, this is open to error if API returns JSON instead of string

```javascript
let expectedTextButGotJSON = {
  type: 'div',
  props: {
    dangerouslySetInnerHTML: {
      __html: 'some arbitrary json',
    },
  },
);

let message = { text: expectedTextButGotJSON };

<p>{ message.text }</p>
```

* React 0.14 tagged every React element with a Symbol
* Can't put Symbol in JSON
* React checks if `$$typeof` and will not process element if property is missing or invalid
* Symbols are also global between environments like iframes and workers

