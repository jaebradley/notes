[Finally understanding how references work in Android and Java](https://medium.com/google-developer-experts/finally-understanding-how-references-work-in-android-and-java-26a0d9c92f83)

* A "strong" reference are "ordinary" references in Java - anytime a new object is created, a strong reference is created by default
* `MyObject object = new MyObject();` - a strong reference is stored in `object`
  * `object` is strongly reachable meaning that it can be reached through a chain of string references
  * This will prevent the Garbage Collector from picking it up and destroying it
* In the case where an inner class is referenced by an outer class, the inner class needs to be accessing the outside class during its entire lifetime
* When the outer class is destroyed the inner class is holding a reference to the outer class and the outer class cannot be collected by the garbage collector

```java
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {   
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        new MyAsyncTask().execute();
    }
    
    private class MyAsyncTask extends AsyncTask {
        @Override
        protected Object doInBackground(Object[] params) {
            return doSomeStuff();
        }
        private Object doSomeStuff() {
            //do something to get result
            return new MyObject();
        } 
    }
}
```

* `WeakReference` is a reference not strong enough to keep an object in memory
* The inner class has a reference to the instance of the outer class through a `WeakReference` member variable
  * When the outer class instance stops existing, since the inner class references it through a `WeakReference` it can be garbage collected
* `SoftReference` is a stronger `WeakReference`
  * "If the object is a `SoftReferenec`, I will decide what to do based on the ecosystem conditions"
