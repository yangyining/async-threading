# Introduction #

IAsyncThreadResponder is ultra simple - all it provides is the following method signature:

```
		function execute():void;
```

# Details #

The IAsyncThreadResponder is very critical in regards to the inner workings of the AbstractAsyncThread class.

For instance, the AbstractAsyncThread class **needs** an `execute()` method. However, since it is an abstract class that should never be instantiated, our only option other than enforcing an interface is to rely on overrides. I personally try to avoid overrides wherever possible, so I went with the approach of requiring a specific base interface class.

Additionally, the AbstractAsyncThread class checks at runtime to see if it is being extended by a class either implementing the IAsyncThreadResponder interface, or an interface extending IAsyncThreadResponder. If this check fails, we throw a runtime exception.