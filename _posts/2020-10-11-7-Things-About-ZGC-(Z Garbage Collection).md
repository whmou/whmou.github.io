---
layout: post
title: 7 Things About JVM ZGC (Z Garbage Collection)
---

#### - What:
1. Since Java 11 (Linux/x64), macOS/Windows: Java 14.
2. Goals:
* Max pause times (1) of a few milliseconds.
* Pause times do not increase with the heap or live-set (2) size.
* Handle heaps ranging from a 8MB to 16TB in size.
3. Features: 
* Concurrent
* Region-based (3)
* Compacting (4)
* NUMA-aware (5)
* Using colored pointers (6)
* Using load barriers (7)


#### - When:
1. You meet the java version / platform conditions.


#### - Notes: 
1. <red_hl>Pause time</red_hl>The pause time is the duration during which the garbage collector <bold_hl>stops the application</bold_hl> and recovers space that is no longer in use.
2. <red_hl>The live set</red_hl> It's the amount of Java heap that is used after an Old Collection (all objects that are not live have been garbage collected).
3. <red_hl>Region-based</red_hl> It's a type of memory management in which each allocated object is assigned to a region. A region, also called a zone, arena, area, or memory context, is a collection of allocated objects that can be efficiently deallocated all at once.
4. <red_hl>Compacting</red_hl> Compaction means moving objects in RAM so that some objects are removed (the dead objects, that the GC is supposed to reclaim) and all remaining objects become contiguous in RAM.
5. <red_hl>NUMA</red_hl> Non-uniform memory access (NUMA): is a computer memory design used in multiprocessing, where the memory access time depends on the memory location relative to the processor. Under NUMA, a processor can access its own local memory faster than non-local memory
6. <red_hl>Colored pointers</red_hl> It's the core concepts of ZGC. It enables ZGC to find, mark, locate, and remap the objects. It doesn't support x32 platforms.
7. <red_hl>Load barrier</red_hl> Applied when loading an object reference from the heap, prevents contention between the GC phase and any application's activity.


#### - Examples:
* Use the -XX:+UnlockExperimentalVMOptions -XX:+UseZGC for the Java options to enable ZGC.

---

##### - References:
[http://cr.openjdk.java.net/~pliden/slides/ZGC-FOSDEM-2018.pdf](http://cr.openjdk.java.net/~pliden/slides/ZGC-FOSDEM-2018.pdf)
[https://wiki.openjdk.java.net/display/zgc/Main](https://wiki.openjdk.java.net/display/zgc/Main)
[https://stackoverflow.com/questions/33803788/java-garbage-collection-fine-tuning-maximum-pause-time-goal](https://stackoverflow.com/questions/33803788/java-garbage-collection-fine-tuning-maximum-pause-time-goal)
[https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/memleaks001.html](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/memleaks001.html)
[https://en.m.wikipedia.org/wiki/Region-based_memory_management](https://en.m.wikipedia.org/wiki/Region-based_memory_management)
[https://en.m.wikipedia.org/wiki/Non-uniform_memory_access](https://en.m.wikipedia.org/wiki/Non-uniform_memory_access)
[http://docs.adaptivecomputing.com/torque/6-0-0/Content/topics/torque/11-Using%20NUMA/NUMAawareTorque.htm#about](http://docs.adaptivecomputing.com/torque/6-0-0/Content/topics/torque/11-Using%20NUMA/NUMAawareTorque.htm#about)
[https://subscription.packtpub.com/book/application_development/9781789133271/8/ch08lvl1sec45/colored-pointers](https://subscription.packtpub.com/book/application_development/9781789133271/8/ch08lvl1sec45/colored-pointers)
[https://stackoverflow.com/questions/2663292/how-does-heap-compaction-work-quickly](https://stackoverflow.com/questions/2663292/how-does-heap-compaction-work-quickly)
[https://www.baeldung.com/jvm-zgc-garbage-collector](https://www.baeldung.com/jvm-zgc-garbage-collector)