---
title: Go Internals
parent: Advanced Topics
nav_order: 2
---

## Chapter 15: Go Internals

Understanding the inner mechanisms of Go can give you deeper insights into how the language operates, allowing you to write more efficient and effective code. This chapter will explore two core aspects of Go's internals: garbage collection and the Go scheduler.

**15.1 Garbage Collection**

Garbage collection (GC) is an automatic memory management process that reclaims memory occupied by objects that are no longer in use by the program. Go's garbage collector is designed to be efficient and to minimize the burden on developers.

*   **How Garbage Collection Works in Go:**
    Go's garbage collector uses a concurrent tri-color mark-and-sweep algorithm, this algorithm runs concurrently with your program, reducing the impact on overall performance. The garbage collector will work as follows:

    1.  **Mark Phase:** The GC starts by scanning all the objects that are directly reachable by a program, such as global variables, function stack and registers.
    2.  **Mark Phase:** Any object pointed by the reachable objects will also be marked as reachable.
	3.  **Mark Phase:** This process will continue until all reachable objects are marked.
    4.  **Sweep Phase:** Once all reachable objects are marked, the GC will sweep the heap, collecting all the unmarked objects and freeing up the memory.
	5.  The garbage collector will periodically run this mark and sweep algorithm based on certain events such as memory allocations.

*   **Go GC Goals:**

    *   **Low Latency:** Go's GC is designed to have low latency, meaning that pauses due to garbage collection are minimal, avoiding disruptions in the program execution.
    *   **High Throughput:** The goal of GC is to recycle unused memory as quickly as possible while keeping a minimum impact to the performance of the main application.
	* **Automatic:** It's done automatically, without any input or control by the developer, simplifying memory management and avoiding errors.

*  **Factors Influencing GC Behavior:**
    *   **Memory Allocation Rate:** A higher memory allocation rate will force the GC to run more often, which can impact the performance of the main application.
	*  **Heap Size**: The total size of the memory used by your application, a bigger heap will require the GC to work harder.
    *   **GC Pacing:** Go's garbage collector dynamically adjusts its frequency based on memory allocation patterns.
    *   **GOMAXPROCS:** The `GOMAXPROCS` environment variable determines the number of OS threads that can execute Go code at the same time, and it can affect the speed of the garbage collection.
*   **Working with Garbage Collection:**
    *   In most cases, Go's garbage collector will handle everything for you automatically.
    *   Try to allocate less objects as possible, as that will decrease the number of times that the GC is invoked.
	*	Use tools like pprof to identify performance issues caused by memory allocations.
    * Avoid using finalizers as they will not be called immediately when the object is no longer in use, leading to memory leaks.
	*  `runtime.GC()` You can use this function to force a garbage collection, but it should be avoided whenever possible.

**15.2 The Go Scheduler**

The Go scheduler is a key component that manages the execution of goroutines, Go's lightweight concurrency mechanism. It's responsible for efficiently multiplexing goroutines onto a limited number of operating system threads.

*   **How the Go Scheduler Works:**

    1.  **M:N Scheduling:** Go uses an M:N scheduling model, where M goroutines are mapped onto N OS threads.
    2.  **Goroutine States:** The scheduler manages goroutines in different states (runnable, running, blocked, etc.).
    3.  **Run Queues:** Each OS thread (M) has its own run queue, which contains runnable goroutines that are ready to execute.
	4.  **Work Stealing:** If a thread runs out of goroutines to execute, it will steal runnable goroutines from other threads queues.
	5. **Preemptive scheduling**: The scheduler can stop the execution of a goroutine at any time and switch to another one. This helps to prevent one goroutine from blocking other goroutines from executing.
    6.  **System Calls:** When a goroutine performs a blocking operation (like I/O or system calls), it's put to sleep and other goroutines are able to execute.

*   **Go Scheduler Goals:**

    *   **Fairness:** The scheduler attempts to be fair to all goroutines, ensuring that each has an opportunity to execute, but it is not guaranteed.
    *   **Efficiency:** The scheduler aims to minimize context switching overhead and maximize CPU utilization by using a work stealing algorithm.
	* **Concurrency:** The scheduler enables concurrent execution of multiple goroutines on a single core, or in multiple cores if available, and scales to run in multiple machines.
    * **Scalability:** The scheduler allows the Go applications to scale to multiple cores or machines, when required.

*  **Factors Influencing Scheduler Behavior:**
	*   **Number of Goroutines**: A larger number of goroutines will require the scheduler to work harder to allocate and run those goroutines.
    *   **Blocking operations:** If goroutines are constantly waiting for I/O operations or system calls, it may decrease the performance of the application, as many threads might be idle, and waiting for blocked goroutines.
    *   **GOMAXPROCS:** The `GOMAXPROCS` env variable determines the number of OS threads that are available for the scheduler, and a bigger number will allow more parallelism.

*   **Working with the Go Scheduler:**

    *   You don't have direct control over the Go scheduler.
    *   Use Go’s concurrency primitives, such as goroutines and channels, to take advantage of the scheduler.
    *   Avoid long-running tasks on a single goroutine that can block other goroutines from running, and use channels to communicate between goroutines.
    *    Use tools such as `pprof` to identify bottlenecks in your code.

**15.3 Key Takeaways**

This chapter has explored two important Go internals:

*   **Garbage Collection:**
    *   An automatic memory management process in Go, that will free memory from unused variables automatically.
    *   Uses concurrent mark-and-sweep algorithm, designed for low latency and high throughput.
	*	You should try to allocate less objects as possible.
	* You can use tools like pprof to identify the sources of the memory allocations.
*   **The Go Scheduler:**
    *   Manages the execution of goroutines using an M:N scheduling model.
    *    Uses a work stealing algorithm to allow efficient execution of the goroutines.
    * You can not control the Go Scheduler, but you should follow best practices for concurrent execution.
    *  You should avoid long-running tasks on a single goroutine, and use channels for communication between goroutines.

Understanding these internals will give you a greater appreciation of how Go operates and will allow you to write higher quality software.

