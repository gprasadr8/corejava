### The G1 Garbage Collector:
The Garbage-First (G1) collector is a server-style garbage collector, targeted for multi-processor machines with large memories. It meets garbage collection (GC) pause time goals with a high probability, while achieving high throughput. The G1 garbage collector is fully supported in Oracle JDK 7 update 4 and later releases. The G1 collector is designed for applications that:

	- Can operate concurrently with applications threads like the CMS collector.
	- Compact free space without lengthy GC induced pause times.
	- Need more predictable GC pause durations.
	- Do not want to sacrifice a lot of throughput performance.
	- Do not require a much larger Java heap.

G1 is planned as the long term replacement for the Concurrent Mark-Sweep Collector (CMS). 

Comparing G1 with CMS, there are differences that make G1 a better solution. G1 is a compacting collector and mostly eliminates potential fragmentation issues. 

Also, G1 offers more predictable garbage collection pauses than the CMS collector, and allows users to specify desired pause targets.

G1 Operational Overview

The older garbage collectors (serial, parallel, CMS) all structure the heap into three sections: young generation, old generation, and permanent generation of a fixed memory size.


![old_gc_heap_structure](old_gc_heap_structure.png)

All memory objects end up in one of these three sections.

The G1 collector takes a different approach.


![g1_gc_heap_structure](g1_gc_heap_structure.png)

The heap is partitioned into a set of equal-sized heap regions, each a contiguous range of virtual memory. Certain region sets are assigned the same roles (eden, survivor, old) as in the older collectors, but there is no fixed size for them(eden, survivor, old).This provides greater flexibility in memory usage.


G1 Footprint
If you migrate from the ParallelOldGC or CMS collector to G1, you will likely see a larger JVM process size. This is largely related to "accounting" data structures such as Remembered Sets and Collection Sets.

Remembered Sets or RSets track object references into a given region. There is one RSet per region in the heap. The RSet enables the parallel and independent collection of a region. The overall footprint impact of RSets is less than 5%.

Collection Sets or CSets the set of regions that will be collected in a GC. All live data in a CSet is evacuated (copied/moved) during a GC. Sets of regions can be Eden, survivor, and/or old generation. CSets have a less than 1% impact on the size of the JVM.

### When to migrate for G1 GC?

The first focus of G1 is to provide a solution for users running applications that require large heaps with limited GC latency. This means heap sizes of around 6GB or larger, and stable and predictable pause time below 0.5 seconds.

Applications running today with either the CMS or the ParallelOldGC garbage collector would benefit switching to G1 if the application has one or more of the following traits.

	- Full GC durations are too long or too frequent.
	- The rate of object allocation rate or promotion varies significantly.
	- Undesired long garbage collection or compaction pauses (longer than 0.5 to 1 second)

**Note:** If you are using CMS or ParallelOldGC and your application is not experiencing long garbage collection pauses, it is fine to stay with your current collector. Changing to the G1 collector is not a requirement for using the latest JDK.

 
