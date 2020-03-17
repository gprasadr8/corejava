## GC Algorithms: 

### GC Algorithms Basic Concepts
Before diving into the practical implementation details of Garbage Collection algorithms it will be beneficial to define the required terminology and to understand the basic principles supporting the implementations.

Specific details vary from collector to collector but in general all collectors focus on two areas
	1. Find out all objects that are still alive
	2. Get rid of everything else – the supposedly dead and unused objects.

First part, the census on live objects, is implemented in all collectors with the help of a process called Marking.

### Marking Reachable Objects:

Every modern GC algorithm used in JVM starts its job with finding out all objects that are still alive. 
This concept is best explained using the following picture representing your JVM’s memory layout:

![Marking Reachable Objects](file:///marking_process.png)

First, GC defines some specific objects as Garbage Collection Roots. Examples of such GC roots are:
	1. Local variable and input parameters of the currently executing methods
	2. Active threads
	3. Static field of the loaded classes
	4. JNI references

- GC traverses the whole object graph in your memory, starting from those Garbage Collection Roots and following references from the roots to other objects. 

- Every object the GC visits is marked as alive.

- Live objects are represented as blue on the picture above. When the marking phase finishes, every live object is marked. 

- All other objects (grey data structures on the picture above) are thus unreachable from the GC roots,implying that your application cannot use the unreachable objects anymore. Such objects are considered garbage.


**There are important aspects to note about the marking phase:**

- The application threads need to be stopped for the marking to happen as you cannot really traverse the graph if it keeps changing under your feet all the time. Such a situation when the application threads are temporarily stopped so that the JVM can indulge in housekeeping activities is called a safe point resulting in a Stop The World pause. Safe points can be triggered for different reasons but garbage collection is by far the most common reason for a safe point to be introduced.

- The duration of this pause depends neither on the total number of objects in heap nor on the size of the heap but on the number of alive objects. So increasing the size of the heap does not directly affect the duration of the marking phase.

- When the mark phase is completed, the GC can proceed to the next step and start removing the unreachable objects.

### Removing Unused Objects

Removal of unused objects is somewhat different for different GC algorithms but all such GC algorithms can be divided into three groups:

1.Sweeping<br>
2.Compacting<br>
3.Copying


1. **Sweep:**

Mark and Sweep algorithms use conceptually the simplest approach to garbage by just ignoring such objects.

What this means is that after the marking phase has completed all space occupied by unvisited objects is considered free and can thus be reused to allocate new objects.

The approach requires using the so called **free-list recording** of every free region and its size. The management of the free-lists adds overhead to object allocation. 

Built into this approach is another weakness – there may exist plenty of free regions but if no single region is large enough to accommodate the allocation, the allocation is still going to fail (with an OutOfMemoryError in Java).


![Sweep Phase before and after](file:///sweep_phase_before_after.png)


2. **Compact:**

Mark-Sweep-Compact algorithms solve the shortcomings of Mark and Sweep by moving all marked – and thus alive – objects to the beginning of the memory region. 

The downside of this approach is an increased GC pause duration as we need to copy all objects to a new place and to update all references to such objects. 

The benefits to Mark and Sweep are also visible – after such a compacting operation new object allocation is again extremely cheap via pointer bumping. Using such approach the location of the free space is always known and no fragmentation issues are triggered either.

![Compact Phase before and after](file:///compact_phase_before_after.png)


3. **Copy:**

Mark and Copy algorithms are very similar to the Mark and Compact as they too relocate all live objects. 

The important difference is that the target of relocation is a different memory region as a new home for survivors.

Mark and Copy approach has some advantages as copying can occur simultaneously with marking during the same phase. 

The disadvantage is the need for one more memory region, which should be large enough to
accommodate survived objects.

![Copy Phase before and after](file:///copy_phase_before_after.png)

### Types of GC Algorithms:

An important aspect to recognize first is the fact that, for most JVMs out there, two
different GC algorithms are needed – one to clean the Young Generation and another to clean the Old Generation.

You can choose from a variety of such algorithms bundled into the JVM. If you do not specify a garbage collection algorithm explicitly, a platform-specific default will be used.

The most used and practical combination of algorithms for young and old generations are

- Serial GC for both the Young and Old generations
- Parallel GC for both the Young and Old generations
- Parallel New for Young + Concurrent Mark and Sweep (CMS) for the Old Generation
- G1 in case of which the generations are not separated between the Young and Old

	|--------------------| ---------------- | --------------------------------------------|
	|	Young            |  Tenured         |  JVM options                                |
	|--------------------| -----------------| --------------------------------------------|
	| Serial 			 |	Serial 			|	-XX:+UseSerialGC                          |
	|					 |					|                                             |
	| Parallel Scavenge  |  Parallel Old 	|	-XX:+UseParallelGC -XX:+UseParallelOldGC  |
	|					 |                  |                                             |
	| Parallel New 		 |	CMS 			|	-XX:+UseParNewGC -XX:+UseConcMarkSweepGC  |
	|				     |                  |                                             |
	| G1 				 |					|	-XX:+UseG1GC                              |
	|					 |                  |                                             |
	|--------------------| -----------------| --------------------------------------------| 


### 1. Serial GC:	
	 