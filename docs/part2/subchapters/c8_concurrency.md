---
title: Concurrency and Parallelism - Threads, Processes, and AsyncIO
parent: Advanced Go Development
nav_order: 6
---

## Chapter 8: Concurrency and Parallelism - Threads, Processes, and AsyncIO

Concurrency is a key feature of Go, enabling you to perform multiple tasks seemingly simultaneously. Go achieves concurrency through goroutines and channels, which provide a lightweight and efficient approach to parallel programming. This chapter will guide you through the core concepts and best practices of concurrency in Go.

**8.1 Goroutines**

Goroutines are lightweight, concurrent functions that run independently from the main program flow. They are the building blocks for concurrency in Go and are much more lightweight than traditional threads.

*   **Creating and Launching Goroutines:**
    To launch a function as a goroutine, use the `go` keyword followed by the function call:
    ```go
    package main

    import (
        "fmt"
        "time"
    )

    func printNumber(n int) {
        for i := 0; i < 5; i++ {
            fmt.Println("Goroutine:", n, "Count:", i)
            time.Sleep(100 * time.Millisecond) // Simulate work
        }
    }
    func main() {
        go printNumber(1) // Launch printNumber as a goroutine
        go printNumber(2) // Launch another printNumber as a goroutine
        
        // Sleep to allow goroutines to execute, otherwise the program may end before the goroutines are executed
        time.Sleep(1 * time.Second)

	    fmt.Println("Program finished.")
    }
    ```
     *   The `go printNumber(1)` and `go printNumber(2)` launch the `printNumber` function as goroutines.
	 *   The `time.Sleep` in the `main` function ensures the program will wait for the goroutines to finish before exiting.
    *   Goroutines run concurrently with the main function.
    *   The program may finish before the goroutines complete if the main function exits.

*   **Understanding Concurrent Execution:**
    Goroutines can run in any order and can be executed in parallel by the Go scheduler. You cannot make any assumptions about the order of execution.
    ```go
    package main

    import (
        "fmt"
        "time"
	    "math/rand"
    )

    func printNumber(n int) {
        for i := 0; i < 5; i++ {
		    fmt.Println("Goroutine:", n, "Count:", i)
		    time.Sleep(time.Duration(rand.Intn(300)) * time.Millisecond) // Simulate different work duration
        }
    }
    func main() {
        go printNumber(1)
        go printNumber(2)
	    go printNumber(3)

        time.Sleep(2 * time.Second)
        fmt.Println("Program finished.")
    }
    ```
	* The order of execution is unpredictable because goroutines may be scheduled in any order.
    * The `rand.Intn` function is used to simulate that goroutines can execute in different timings, which will show that they will not execute in order.

*   **The `go` Keyword:**
    The `go` keyword is used to start a new goroutine, executing a function in concurrent way.

**8.2 Channels**

Channels are typed conduits through which goroutines can communicate and synchronize. They provide a safe and efficient way for goroutines to exchange data.

*   **Creating and Using Channels for Communication:**
	Channels are created with the `make` function and the `chan` keyword.
	```go
	package main

    import (
	    "fmt"
	    "time"
    )

	func sendData(c chan string) {
		time.Sleep(1 * time.Second) // simulate some work
		c <- "Hello from goroutine"
	}

    func main() {
	   c := make(chan string); // create a channel that sends strings
	   go sendData(c);
	   
	   msg := <- c // receive value from channel

        fmt.Println("Message received:", msg) // Output: Message received: Hello from goroutine
	}
    ```
    *   `make(chan string)` creates a channel that transmits string values.
    *   `c <- "Hello from goroutine"` sends the value to the channel.
    *   `msg := <- c` receives the value from the channel.

*   **Buffered vs. Unbuffered Channels:**
    *   **Unbuffered Channels:** By default channels are unbuffered, meaning that senders will block until someone receives the value and the receiver will block until a value is sent to the channel.
    ```go
    package main

    import (
	    "fmt"
	    "time"
    )

    func sendData(c chan string) {
	    time.Sleep(1 * time.Second)
	    c <- "First message"; // will block until a receiver is ready
	    fmt.Println("First message sent")
	    time.Sleep(1 * time.Second)
	    c <- "Second message"; // will block until a receiver is ready
		fmt.Println("Second message sent")
    }

    func main() {
	    c := make(chan string);
	    go sendData(c);
	    time.Sleep(500 * time.Millisecond);
	    msg := <- c // this will receive first message, then proceed with the second one.
	    fmt.Println("Message received:", msg); // Output: Message received: First message
	    msg = <- c
	    fmt.Println("Message received:", msg);  // Output: Message received: Second message
    }
    ```
    *  In the above example, the `sendData` function is sending a value to the channel. The program will stop at this line until a receiver is available. The receiver will receive the value, and the program will continue. If there are no receivers, the sender will be indefinitely blocked.
    *   **Buffered Channels:** Buffered channels can hold a limited number of values, allowing the sender to send multiple values to a channel without waiting for a receiver. Buffered channels can be created by specifying the buffer size in the `make` function: `make(chan string, 10)`.

    ```go
    package main

    import (
	    "fmt"
	    "time"
    )

    func sendData(c chan string) {
	    c <- "First message";
	    fmt.Println("First message sent")
	    c <- "Second message";
	    fmt.Println("Second message sent")
    }

    func main() {
	    c := make(chan string, 2);
	    go sendData(c);
	    time.Sleep(500 * time.Millisecond);
	    msg := <- c // this will receive the first message
	    fmt.Println("Message received:", msg);
	    msg = <- c // this will receive the second message
	    fmt.Println("Message received:", msg);
    }
    ```
    *   In the above example, the channel can store 2 values before blocking the sender.

*   **Channel Directionality:**
    You can specify the direction of a channel:
    *   `chan<- int`: A send-only channel for `int` values.
    *   `<-chan int`: A receive-only channel for `int` values.
    *   `chan int`: A channel that can be both send and receive for `int` values.
   ```go
    package main

    import "fmt"

    func sendData(c chan<- string, msg string) {
        c <- msg // Sending data to the channel
    }

    func receiveData(c <-chan string) {
        msg := <- c // receiving from the channel
        fmt.Println("received: ", msg);
    }

    func main() {
        c := make(chan string)

        go sendData(c, "Hello, world!") // Calling sendData with a send only channel
        go receiveData(c); // calling receiveData with a receive only channel
        
	    // sleeping for one second to make sure that goroutines are executed
		var input string
        fmt.Scanln(&input)
    }
    ```
    *   The function `sendData` receives a send-only channel, and is only allowed to send data through it.
    *   The function `receiveData` receives a receive-only channel, and is only allowed to receive data from it.
    *   This will prevent accidental wrong usage of the channels, for example sending data through a receive-only channel.

*   **Patterns for Concurrency:**
    There are many concurrency patterns such as:
	*   **Worker Pool:** Launching a pool of goroutines to handle incoming jobs from a channel.
		```go
	    package main

	    import (
		    "fmt"
		    "time"
		    "math/rand"
	    )

	    func worker(id int, jobs <-chan int, results chan<- int) {
		    for j := range jobs {
			fmt.Println("worker", id, "processing job", j);
			time.Sleep(time.Duration(rand.Intn(500)) * time.Millisecond) // simulate work
			results <- j * 2
		    }
	    }

	    func main() {
		    jobs := make(chan int, 100)
		    results := make(chan int, 100)

		    for i := 0; i < 3; i++ {
			go worker(i, jobs, results);
		    }

		    for i := 0; i < 10; i++ {
			jobs <- i;
		    }

		    close(jobs); // tell workers there are no more jobs

		    for i := 0; i < 10; i++ {
			result := <- results
			fmt.Println("received: ", result);
		    }
	    }
		```
		*   In the above example, we have 3 workers that are consuming jobs from the `jobs` channel and putting the results in the `results` channel.
	*   **Fan-out, Fan-in:** Distributing work to multiple goroutines and combining the results from multiple channels.

**8.3 Mutexes (Synchronization)**

Mutexes provide a mechanism for synchronizing access to shared resources, ensuring that only one goroutine can access the resource at a time, and preventing race conditions.

*   **Preventing Race Conditions:**
    A race condition occurs when multiple goroutines access and modify a shared resource concurrently, leading to unpredictable and erroneous results.

    ```go
    package main

    import (
        "fmt"
        "sync"
        "time"
    )

    var counter int // shared resource, accessing it concurrently will cause a race condition
    var wg sync.WaitGroup

    func incrementCounter(i int) {
	    defer wg.Done()
	    for i := 0; i < 1000; i++ {
	      	counter++
		fmt.Println("goroutine:", i, "counter:", counter);
	    }
    }

    func main() {
        wg.Add(2)
        go incrementCounter(1);
	go incrementCounter(2);
        wg.Wait()
        fmt.Println("Final counter:", counter) // this will return a random number due to the race condition.
    }
    ```
    *   The `counter` variable is a shared resource that is accessed by two goroutines.
	*   Each goroutine will increment the `counter` variable multiple times.
    *   Because the order of execution is unpredictable, a race condition will happen, where the value of `counter` can become any number depending on which goroutine is executed first.

*   **Using `sync.Mutex`:**
    Use `sync.Mutex` to lock and unlock a shared resource and prevent race conditions.
    ```go
    package main

    import (
	    "fmt"
	    "sync"
	    "time"
    )

    var counter int // shared resource, accessing it concurrently will cause a race condition
    var wg sync.WaitGroup;
    var m sync.Mutex; // declare the mutex variable

    func incrementCounter(i int) {
	    defer wg.Done();
	    for i := 0; i < 1000; i++ {
		m.Lock()  // Lock access to the shared resource
		counter++
		fmt.Println("goroutine: ", i, "counter: ", counter);
		m.Unlock(); // Unlock access to the shared resource
		time.Sleep(100 * time.Microsecond) // simulating some work
	    }
    }

    func main() {
        wg.Add(2)
        go incrementCounter(1);
	go incrementCounter(2);
	wg.Wait();

        fmt.Println("Final counter:", counter) // The output will always be 2000.
    }
    ```
    *   `m.Lock()` locks the mutex, preventing other goroutines from accessing the shared resource.
    *   `m.Unlock()` unlocks the mutex, allowing other goroutines to access the resource.

**8.4 WaitGroup**

The `sync.WaitGroup` is used to wait for a collection of goroutines to complete their execution.

*   **Synchronizing the Execution of Goroutines:**
    ```go
    package main

    import (
        "fmt"
        "sync"
        "time"
    )

    func worker(id int, wg *sync.WaitGroup) {
        defer wg.Done()
        fmt.Println("Worker:", id, "starting")
        time.Sleep(1 * time.Second)
        fmt.Println("Worker:", id, "done")
    }

    func main() {
        var wg sync.WaitGroup
        wg.Add(3)
        go worker(1, &wg)
        go worker(2, &wg)
        go worker(3, &wg)
        wg.Wait()
        fmt.Println("All workers done")
    }
    ```
    *   `wg.Add(3)` adds three goroutines to the wait group.
    *   `defer wg.Done()` signals that a goroutine has finished its work.
    *   `wg.Wait()` blocks until all goroutines have finished.

**8.5 Key Takeaways**

This chapter has covered the core concepts of concurrency in Go:

*   **Goroutines:** Lightweight, concurrent functions launched using the `go` keyword. Goroutines do not execute in a predictable order.
*   **Channels:** Typed communication channels between goroutines. They can be buffered or unbuffered, and they can have directionality.
*   **Mutexes:** Used for synchronizing access to shared resources and preventing race conditions. Only one goroutine can lock a mutex at a time.
*   **WaitGroup:** Used to wait for a collection of goroutines to finish their execution.

By mastering these concepts, you can build highly concurrent and efficient Go applications.
