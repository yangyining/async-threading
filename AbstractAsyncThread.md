# Introduction #

The AbstractAsyncThread class provides the basis for threaded objects within the AsyncThreading library.

However, it is an **abstract** class, in that it must be extended to be used. If it is instantiated directly, it will yield a run time exception. Additionally, the extended class must implement either the IAsyncThreadResponder interface, or implement a class extending the IAsyncThreadResponder interface. The reason for this architectural decision was primarily based on removing dependencies on overrides. Additionally, the type of interface the thread instance uses determines the behavior of the thread - ie: whether or not it is scalable.

# Thread Priority Levels #

A thread's priority level is at the most basic level an integer specifying the nth frame for the thread to execute on in a given second.

For example: Flex's default frame rate is 24 frames per second. If you assign a thread a priority value of 8, the thread is going to execute on every 8th frame, or 3 times per second.

The AbstractAsyncThread class provides several statics default values for thread priority:
```
		public const RUN_LEVEL_REAL_TIME:int = 1;
		public const RUN_LEVEL_HIGH:int = 3;
		public const RUN_LEVEL_ABOVE_NORMAL:int = 6:
		public const RUN_LEVEL_NORMAL:int = 8;
		public const RUN_LEVEL_BELOW_NORMAL:int = 12:
		public const RUN_LEVEL_LOW:int = 24;
```

## Adjusting the priority ##


You can adjust a thread's priority at any point in its lifecycle - even while it is running.

Example:

```
package {
	import com.symantec.premiumServices.asyncThreading.abstract.AbstractAsyncThread;
	import com.symantec.premiumServices.asyncThreading.interfaces.IAsyncThreadResponder;
	
	public class RealTimeWorkerBee extends AbstractAsyncThread implements IAsyncThreadResponder {
		
		public function RealTimeWorkerBee() {
			super();
			super.priority = super.RUN_LEVEL_REAL_TIME;
			super.start();
		}
		
		public function execute():void {
			//
		}
		
	}
}
```

The main thing to note in the above example is this line:
`super.priority = super.RUN_LEVEL_REAL_TIME;`

And here is a more complicated example:
```
package {
	import com.symantec.premiumServices.asyncThreading.abstract.AbstractAsyncThread;
	import com.symantec.premiumServices.asyncThreading.interfaces.IAsyncThreadScalableResponder;
	
	public class RealTimeWorkerBee extends AbstractAsyncThread implements IAsyncThreadScalableResponder {
		
		public function RealTimeWorkerBee() {
			super();
			super.priority = super.RUN_LEVEL_REAL_TIME;
			super.start();
		}
		
		public function execute():void {
			//
		}
		
		public function increaseWorkLoad():void {
			if (super.priority > 1) {
				super.priority = super.priority - 1;
			}
		}
		
		public function decreaseWorkLoad():void {
			if (super.priority < 24) {
				super.priority = super.priority + 1;
			}
		}
		
	}
}
```

In this example, we have a thread object whose priority is directly controlled by how well the application is performing, via the IAsyncThreadScalableResponder API. Cool!

# Thread Communication #

Threads do not have direct access to eachother, but they **can** dispatch messages to the threading system as a whole.

## Dispatching Thread Messages ##

In this example, the DataBuilderThread is calling
`super.sendThreadMessage("realTimeDataReady", binaryData);`
in its execute() method. presumably, there would be some work done on the data before it is sent.

```
package {
	import com.symantec.premiumServices.asyncThreading.abstract.AbstractAsyncThread;
	import com.symantec.premiumServices.asyncThreading.interfaces.IAsyncThreadResponder;
	
	import flash.utils.ByteArray;

	public class DataBuilderThread extends AbstractAsyncThread implements IAsyncThreadResponder {
		
		protected var iterationCount:int = 20000;
		protected var binaryData:ByteArray = new ByteArray();
		
		public function DataBuilderThread() {
			super();
			super.priority = super.RUN_LEVEL_REAL_TIME;
			super.start();
		}
		
		public function execute():void {
			super.sendThreadMessage("realTimeDataReady", binaryData);
		}
		
	}
}
```

The basic signature is like so:
`super.send("message_name", *);`

## Receiving Thread Messages ##

The AsyncThreading library manages message callbacks with reflection-based mappings. This is very similar to the methodology used in Fabrication for PureMVC.

For example... if we have a thread class called DataReaderThread, we may want to respond to the "realTimeDataReady" message from the DataBuilderThread:

```
package {
	import com.symantec.premiumServices.asyncThreading.abstract.AbstractAsyncThread;
	import com.symantec.premiumServices.asyncThreading.interfaces.IAsyncThreadResponder;
	
	import flash.utils.ByteArray;

	public class DataReaderThread extends AbstractAsyncThread implements IAsyncThreadResponder {
		
		public function DataReaderThread() {
			super();
			super.priority = super.RUN_LEVEL_LOW;
			super.start();
		}
		
		public function execute():void {
			//
		}

          	public function respondToRealTimeDataReady(message:IAsyncThreadMessage):void {
			//
		}
		
	}
}
```

Incoming thread messages are queued up and executed sequentially immediately before the thread's next execution phase.