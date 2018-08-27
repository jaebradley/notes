# [Tale of 5 Callbacks](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch2.md#tale-of-five-callbacks)

* eCommerce checkout system that uses 3rd party analytics function that takes a callback that will charge customer credit card and then displays credit card

```javascript
analytics.trackPurchase(purchaseData, function() {
  chargeCreditCard();
  displayThankYouPage();
});
```

* Six months late code causes credit card to get charged five times in a row
* Analytics library was changed to retry the callback
* In order to protect against these types of changes a latch is created

```javascript
var tracked = false;

analytics.trackPurchase(purchaseData, function() {
  if (!tracked) {
    chargeCreditCard();
    displayThankYouPage();
  }
});
```
