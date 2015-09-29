# Introduction #
AsyncThreadingManager is the singleton manager class which sits in the middle of all of the various components of the AsyncThreading framework.

It has several key features, including the ability to send messages to all all executing threads from outside of the AsyncThreading framework. Additionally, you can terminate all executing threads, as well as shut down the AsyncThreading system as a whole.


# Public static vars #

### verboseLogging ###

This value defaults to false. 95% of all of the logging in the AsyncThreading framework is controlled by this.

To enable it, do this:

```
AsyncThreadingManager.verboseLogging = true;
```

Unless you have a TraceTarget defined, you are not going to see the logging. That is implemented like so:

```
			public function creationComplete():void {
				var tracer:TraceTarget = new TraceTarget();
					tracer.includeTime = true;
					tracer.includeDate = true;
					tracer.includeCategory = true;
					tracer.includeLevel = true;
					
				Log.addTarget( tracer );
				
				AsyncThreadingManager.verboseLogging = true;
			}
```

### useDistributedFrameLoading ###

This value defaults to true. When this value is true, the AsyncThreading system load balances thread execution by giving every registered thread a seed which offsets the frame it is executed on.

For example, if you have 3 threads that all have priority settings of `RUN_LEVEL_ABOVE_NORMAL`, or 6, they will all execute on the same frame every time. This could lead to predictable lowpoints in your applications performance. However, if `useDistributedFrameLoading` is enabled (which it is by default), they will be assigned random seeds, which means that they will ultimately execute on different frames from eachother. They will still have a priority of 6 and execute on every 6th frame, but their **start** frames will be different.

This could definitely lead to threads being out of synch with each other, but the prevailing idea is that threads will not be crunching on shared data so this should not be an issue. However, if it does become an issue, you can disable it like so:

```
AsyncThreadingManager.useDistributedFrameLoading = false;
```

# Monitoring Display Objects #

The AsyncThreading system automatically monitors your application's stage reference for framerate. However, we have discovered that nested Flash assets can have their framerates get out of synch with that of the stage.

For example, we had an instance where the stage framerate was reporting as 24fps, but an embedded Flash asset was evaluating at < 18 fps, which made it look choppy (the framerate for the Flash project file was 24fps, just like Flex).

To compensate for this, the AsyncThreading system provides the ability to monitor multiple DisplayObjects outside of the stage reference.

### Adding a DisplayObject to be Monitored ###

Example:

```
			public function monitorTestUI():void {
				AsyncThreadingManager.getInstance().monitorDisplayObject(testUI);
			}
```

### Removing a DisplayObject from the Monitoring System ###

Example:

```
			public function stopMonitorTestUI():void {
				AsyncThreadingManager.getInstance().stopMonitoringDisplayObject(testUI);
			}
```

# Messaging #

It is possible to send messages into the AsyncThreading framework from outside of it. All you need is a message **name** and the data you want to send. Ideally, you have an AbstractAsyncThread instance implemented that is ready to receive that message.

Example:

```
AsyncThreadingManager.getInstance().sendMessageToThreads("moveLeft", 50);
```

Internally, the body (or data) of the message is tracked as an untyped object, so you can pass in whatever you like.


# Utility Methods #

### killAllThreads() ###

If you need to terminate all of the threads that are currently executing, you do it like so:

```
AsyncThreadingManager.getInstance().killAllThreads();
```

This will iterate over the list of threads and terminate them in a sequential fashion. If one is not able to be terminated, an error is logged to the console (if verboseLogging is enabled).

### shutDown() ###

If you need to shut down the AsyncThreading system as a whole, you do it like so:

```
AsyncThreadingManager.getInstance().shutDown();
```

The AsyncThreadingManager will terminate all threads, remove all DisplayObject FPS observers, and clean up it's memory footprint.