# Introduction #

The IAsyncThreadScalableResponder interface extends the IAsyncThreadResponder interface, and offers the following additional method signatures:

```
		function increaseWorkLoad():void;
		function decreaseWorkLoad():void;
```


# Details #

The AsyncThreading framework automatically detects registered AbstractAsyncThread instances that either implement the IAsyncThreadScalableResponder or implement an interface extending IAsyncThreadScalableResponder.

When it detects threads of this type, it automatically attempts to manage the thread objects workload by calling the `increaseWorkLoad()` or `decreaseWorkLoad()` methods depending on the current state of the application.

For example - if the application is running slowly (ie: lower framerate), the AsyncThreading library will attempt to call the `decreaseWorkLoad()` method on any thread objects that support that method signature.

Similarly, if the application is running smoothly, and the thread is performing within acceptable thresholds, we automatically invoke the `increaseWorkLoad()` method on it.