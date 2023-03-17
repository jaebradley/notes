# [Keep It Simple With The Strategy Design Pattern](https://blog.bitsrc.io/keep-it-simple-with-the-strategy-design-pattern-c36a14c985e9)

* Basically, there's a family of algorithms and it selects one from the pool for use at runtime
  * The algorithms should be interchangeable / substitutable (i.e they have the same interface)
  * It prevents hard-wiring of all algorithms into the program - look for conditional statements around related algorithms
* Example is if there's a car wash with three levels of wheel and body washing
* Might end up implementing something like this based on the "wash level"

```javascript
class CarWash {
    washCar(washLevel: Number) {
        switch(washLevel) {
            case 1: 
                    new BasicBodyCleaningFactory().clean()
                    new BasicWheelCleaningFactory().clean()
                break;
            case 2: 
                    new BasicBodyCleaningFactory().clean()
                    new ExecutiveWheelCleaningFactory().clean()
                break;
            case 3: 
                    new ExecutiveBodyCleaningFactory().clean()
                    new ExecutiveWheelCleaningFactory().clean()
                break;
        }
    }
}
```

* This violates the Open / Closed Principle as every new wash level will add another conditional
* Instead, if there was an interface for the cleaning methods (like one for wheel cleaning and the other for body cleaning) and then three different concrete classes that implemented this interface, it would be a lot more straight-forward
  * `CarWash` would take a `CleaningStrategy` interface and would call the cleaning methods on the interface in it's `washCar` method
  * Implementation-wise it would look like `new CarWashProgram(new GoldWashFactory())` or `new CarWashProgram(new BronzeWashFactory())` where the concrete class that implements the shared interface might change

## Authentication Example

```javascript
class BasicAuth {}
class DigestAuth {}
class OpenIDAuth {}
class OAuth {}
class AuthProgram {
    runProgram(authStrategy:any, ...) {
        this.authenticate(authStrategy)
        // ...
    }
    authenticate(authStrategy:any) {
        switch(authStrategy) {
            if(authStrategy == "basic")
                useBasic()
            if(authStrategy == "digest")
                useDigest()
            if(authStrategy == "openid")
                useOpenID()
            if(authStrategy == "oauth")
                useOAuth()
        }
    }
}
```

* Same type of thing as before, where an interface is created that all auth strategies must implement

```javascript
interface AuthStrategy {
    auth(): void;
}
class Auth0 implements AuthStrategy {
    auth() {
        log('Authenticating using Auth0 Strategy')
    }
}
class Basic implements AuthStrategy {
    auth() {
        log('Authenticating using Basic Strategy')
    }
}
class OpenID implements AuthStrategy {
    auth() {
        log('Authenticating using OpenID Strategy')
    }
}

class AuthProgram {
    private _strategy: AuthStrategy
    use(strategy: AuthStrategy) {
        this._strategy = strategy
        return this
    }
    authenticate() {
        if(this._strategy == null) {
            log("No Authentication Strategy set.")
        }
        this._strategy.auth()
    }
    route(path: string, strategy: AuthStrategy) {
        this._strategy = strategy
        this.authenticate()
        return this
    }
}
```

* The `use` method set the authentication strategy and the `AuthProgram#authenticate` delegates to the strategy's auth method
  * Example: `new AuthProgram().use(new OpenID()).authenticate()`