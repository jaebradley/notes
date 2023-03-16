# [CompletableFuture : A Simplified Guide to Async Programming](https://medium.com/swlh/completablefuture-a-simplified-guide-to-async-programming-41cecb162308)

## Fork-Join Framework

* Several worker threads in a common thread pool called ForkJoinPool
* Combines interim results of subtasks executed in worker threads to produce a result
* Workstealing algorithm allows idle threads to steal from busy threads
  * Idle threads randomly choose another thread's queue of tasks and start stealing tasks
  * Continues until all queues among all threads are empty

## Future Interface

* Models an asynchronous computation and provide a reference to a result that is available only when the computation is completed
* Delegates long-lasting operation to a separate thread while the main thread continues focusing on other tasks
* When the main thread cannot proceed without the result of the asynchronous operation, it retrieves it from the Future by invoking its `get` method
  * `get` will retrieve the result or block the main thread until the result is available (or until the time limit is exceeded if the `get` method with the timeout value is exceeded so as not to block forever)

## CompletableFuture

### Chaining multiple CompletableFutures: Non-Blocking

```java
public void bookMyShow(ShowDetails showDetails, String promoCode)
{
CompletableFuture result =                 getMovieList(showDetails.getShowTime().getDay())
            .thenCompose(movies -> selectMovie(movies))
            .thenCompose(movie ->                     selectSeats(showDetails.getShowTime())
            .thenApply(showDetails1 -> applyPromoCode(showDetails1,promoCode))
            .thenCompose(showDetails2 -> getTicketPrice(showDetails2)));
}
```

* The output of one future is used as the input to the next in the chain
* `thenApply` vs. `thenCompose` is like `map` vs. `flatMap` where `thenCompose` and `flatMap` return a "flattened" result

```java
CompletableFuture<String> getPopCorn(){
    CompletableFuture<String> future =  CompletableFuture.supplyAsync(() -> {
          return("Popcorn ready");
   });
    return future;
}
CompletableFuture<String> getDrink(){
    CompletableFuture<String> future =    CompletableFuture.supplyAsync(() -> {
        return("Drink ready");
    });
    return future;
}
public String snackReady(){
       return "Order is ready - Enjoy your movie snacks";
}
//snacks are ready when popcorn and drink are ready
public void getSnacksForMovie(){
    CompletableFuture snacks = getPopCorn()
            .thenCombine(getDrink(),(str1,str2)->{return snackReady();}) ;
}
```

* Multiple independent futures running in parallel
* `thenCombine` takes another `CompletionStage` and a supplier function that executes the two results as arguments
* Bothe `getPopCorm` and `getDrink` are executed in parallel and when both have completed, they are passed as input to the supplier function
