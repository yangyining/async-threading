# Introduction #

AsyncThreading is an actionscript library which facilitates threading within Flex.


# Background #

AsyncThreading was developed for usage within the Premium Services team at Symantec. We specialize in developing rich client-side applications which are a blend of Flex and c++ solutions.

We frequently ran into situations where we had to process large amounts of data within Flex, which, due to the fact that the AVM has only one thread, locked the UI for up to 30 seconds at a time. The obvious solution to this is to chunk the data processing into more manageable time slice objects. This process lead to the creation of an over-arching solution to time slice management, which eventually turned into the AsyncThreading library.

The 1.0 release of AsyncThreading was only used internally, and had several performance issues.

The 2.0 release (which has been approved for opensource) has a much improved performance footprint, and has a better API for scalable threads. Also, we have added messaging between threads.

The 2.0.x code base is actively used in our projects. Updates to that code base will be posted here as they are production-certified.