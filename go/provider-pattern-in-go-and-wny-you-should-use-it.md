# [Provider Pattern in Go and Why You Should Use It](https://medium.com/swlh/provider-model-in-go-and-why-you-should-use-it-clean-architecture-1d84cfe1b097)

* Example is an application that uses a 3rd party weather service
* There is a `WeatherProvider` `interface` that has a single method (`GetWeatherByCity`)
* Inject the dependency / interface into the application - the application / service does not care what entity supplies the data or how it is generated
* The "provider" is an adapter between the 3rd party service and the application
  * Create an `openweather` package - which signifies that the logic doesn't have to do with business / application logic but with logic surrounding a 3rd party service
  * Create a `struct` for the response from the 3rd party service - convert the response struct to the general application `Weather` data struct
    * This allows 3rd party specifics (like the `JSON` key names) that can then convert to the general application / service details
    * So if the 3rd party changed it's response, only the adapater / provider implementation would need to change
* `OpenWeather` should only appear in the `main` and `openweather` packages
* The interface is called `WeatherProvider` and not `OpenWeatherProvider` - the application doesn't know how the weather data is introduced into the application
* The only component aware of OpenWeather's API is the provider (which is also aware of how to communicate with OpenWeather - no other package should account for HTTP requests, for example)
* By creating the provider interface, can start building / testing the application without waiting for the external service to be available by stubbing the external service
  * Unit testing is easy since the application relies on an interface and not actual implementation
