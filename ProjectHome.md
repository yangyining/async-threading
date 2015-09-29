The Actionscript Virtual Machine (AVM) in the Flash Player is severely limited by only having one thread. To resolve this, AsyncThreading creates and manages a series of "thread" objects which have adjustable priority. The 'priority' settings really just effect every nth frame that the thread runs on. In that sense, the thread objects that the AsyncThreading library instantiates and maintains are more or less just time slices, but for the sake of naming simplicity, we refer to them as threads.

Additionally, there are a few other interesting features in the AsyncThreading library:
  * Dynamically scalable thread objects. These threads can increase or decrease their workload based on the performance of the application. The AsyncThreading library measures how much of an impact it is having on the application and adjusts the threads as necessary.
  * Reflection-based messaging between threads. Implementation is very similar to Fabrication for PureMVC.

Some examples of how we have internally used this library include the following:
  * analysis of large sets of xpath rules
  * iterating over large ByteArray collections and encoding the contents to the correct character set.

If you would like to report a defect or request an enhancement, click the 'Issues' tab and read the existing issues. If an existing issue is relevant, you can add a comment to it or star it. A star helps us understand the issues that users care about most. And, you can get email notifications of updates to starred issues by clicking on 'My Profile' and opting into those notifications. If no existing issue is relevant, you may enter a new one.

For more information, please refer to the wiki pages.

For questions or comments, please email me at [ben.rimbey@gmail.com](mailto:ben.rimbey@gmail.com).
Thanks!



---


# Updates to release 2.1: #

  * intermittent issue with thread message failures resolved
  * messaging behavior updated to only take one message of a given message type per execution cycle. for example: if a message is pinged 9 times with a message before it executes, it will execute against the last message of that message type it has received. another way of looking at it is that it works against the newest data it has received.
  * object instantiation count optimized
  * memory optimizations