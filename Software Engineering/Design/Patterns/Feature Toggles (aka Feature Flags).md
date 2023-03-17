# [Feature Toggles (aka Feature Flags)](https://martinfowler.com/articles/feature-toggles.html)

## Making a flag dynamic

* Idea of a "Toggle Router", which can be implemented as an in-memory store or a highly sophisticated distributed system

```javascript
function createToggleRouter(featureConfig){
  return {
    setFeature(featureName,isEnabled){
      featureConfig[featureName] = isEnabled;
    },
    featureIsEnabled(featureName){
      return featureConfig[featureName];
    }
  };
}
```

## Getting ready to release

* Could have a Toggle Configuration that is environment-specific, and only turn on new feature in pre-prod environment
* Toggle Configuration that can be modified at runtime via some admin UI and use the admin UI to turn on the feature in the test environment
* Toggle Context that uses a cookie or HTTP header to make dynamic, per-request toggling decisions
* Can create a cohort of canary users by randomly sampling X% of the user base by taking modulo of user ID (for example)

## Categories of toggles

### Release Toggles

* Allow incomplete / un-tested codepaths to be shipped to production as latent code which may never be turned on

### Experiment Toggles

* Used to perform things like A/B testing
* Each user of the system is placed into a cohort and at runtime, the Toggle Router will consistently send a given user down one codepath or another, based on cohort

### Permissioning Toggles

* Distinction between permissioning toggles (often used for premium features) vs. Canary Release, is that Canary Release is exposed to a randomly selected cohort of users
* Permissioning Toggles may be very long-lived compared to other categories of Feature Toggles (multiple years)
* Since permissions are user-specific, the toggling decision for a Permissioning Toggle will always be per-request so toggle is very dynamic

### Managing different categories of toggles

#### static vs. dynamic toggles

* The router for an Experiment Toggle makes routing decisions dynamically for a given user, using some sort of consistent cohorting algorithm based on user id
  * Instead of reading some static toggle configuration state, the toggle router will need to read cohort configuration defining things like how large the experimental cohort and control cohort should be

## Implementation Techniques

### De-coupling decision points from decision logic

* One common mistake is to couple the place where a toggling decision is made (the Toggle Point) with the logic behind the decision (the Toggle Router)

```javascript
if( features.isEnabled("next-gen-ecomm") ){ 
  return addOrderCancellationContentToEmail(baseEmail);
} else{
  return baseEmail;
}
```

* Approach is brittle - for example, can't turn on some part of the next-gen feature set without exposing order cancellation (or vice versa)

```javascript
function createFeatureDecisions(features){
  return {
    includeOrderCancellationInEmail(){
      return features.isEnabled("next-gen-ecomm");
    }
    // ... additional decision functions also live here ...
  };
}
```

* There is a decision method on the `FeatureDecisions` object for each specific toggling decision in the code
* Singular place to manage the decision logic
* The call site for these feature decision does not need to know how / why a specific toggling decision is made

### Inversion of Decision

* As feature flagging has a tendency to become more and more prevalant in a system over time, more and more modules will become coupled to the feature flagging system as a dependency
* Can decouple by adding the feature decision object as a dependency to the "client" module (in the example, the Invoice Emailer), and then have a feature-aware factory method that generates the Invoice Emailer with the feature-decision object / logic

```javascript
  function createFeatureAwareFactoryBasedOn(featureDecisions){
    return {
      invoiceEmailler(){
        return createInvoiceEmailler({
          includeOrderCancellationInEmail: featureDecisions.includeOrderCancellationInEmail()
        });
      },
  
      // ... other factory methods ...
    };
  }
```

* The feature decisions object is injected as a dependency at construction time
* Invoice Emailer just knows that some aspects of its behavior can be configured at runtime
* Makes testing Invoice Emailer easier as can test the way that it generates emails both with and without order cancellation, by passing different configuration options during separate tests

### Avoiding conditionals

* Can use a strategy pattern approach by passing some type of modifier (like a function) to the Invoice Emailer that it will use when generating an invoice email
* The modifier will be defined in the factory object that creates the Invoice Emailer based on the feature decision object
  * For example, if order cancellation emails should be included, then the Invoice Emailer is passed an `addOrderCancellationContentToEmail` function to call when generating the invoice email
  * In the case where the behavior is not enabled, an identity function is used instead

```javascript
function createFeatureAwareFactoryBasedOn(featureDecisions){
  return {
    invoiceEmailler(){
      if( featureDecisions.includeOrderCancellationInEmail() ){
        return createInvoiceEmailler(addOrderCancellationContentToEmail);
      }else{
        return createInvoiceEmailler(identityFn);
      }
    },

    // ... other factory methods ...
  };
}
```

## Toggle Configuration

### Toggle Configuration File

* Can now re-configure a feature flag by simply changing that file rather than re-building application code itself
* Will need to perform a re-deploy in order to re-configure a flag

### Distributed Toggle Configuration

* Special-purpose hierarchical key-value stores for managing application configuration (Zookeeper, etcd)
* Form a distributed cluster which provides a shared source of environmental configuration for all nodes attached to the cluster
* Configuration can be modified dynamically whenever required and all nodes in the cluster are automatically informed of the change

### Overriding configuration

* Any environment-specific overriding runs counter to the CD ideal of having the same exact bits and configuration flow all the way through the delivery pipeline

#### Per-request overrides

* Allow toggle's state to be overridden on a per-request basis via a special cookie, query param, HTTP header
* If a service is load-balanced, can be confident that the override will be applied no matter which service instance you are hitting
* Curious / malicious end-users may modify feature toggle states themselves

## Where to place your toggle

### Toggles at the edge

* For per-request context toggles, it makes sense to place Toggle Points in the edge services of your system (the publicly exposed web apps that present functionality to end users)
  * Where user's individual requests enter application domain and where Toggle Router has the most context
  * Also makes sense when controlling access to user-facing features that are not ready to launch yet

