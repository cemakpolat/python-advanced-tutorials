---
title: Other Applications
parent: Real-World Applications
nav_order: 2
---

## Chapter 12: Other Applications

Go's performance, concurrency, and system-level capabilities make it suitable for a wide variety of applications. This chapter will explore three areas where Go shines: log analysis, task queues, and system monitoring.

**12.1 Log Analysis**

Log analysis tools are essential for understanding application behavior, debugging issues, and identifying trends. Go's performance and concurrency make it well-suited for processing large log files efficiently.

*   **Core Functionality:**
    *   Read and parse log files.
    *   Filter log entries based on keywords.
    *   Count log entries by severity or other criteria.
    *   Provide summary statistics or identify potential issues.
*   **Implementation:**

    ```go
	// main.go
	package main

	import (
		"bufio"
		"fmt"
		"os"
		"regexp"
		"strings"
		"strconv"
	)

	func analyzeLogs(filename string, filter string) (map[string]int, error) {
		file, err := os.Open(filename)
		if err != nil {
			return nil, err
		}
		defer file.Close();

		severityCounts := make(map[string]int)
		
		scanner := bufio.NewScanner(file);
		regex := regexp.MustCompile(filter);

		for scanner.Scan() {
			line := scanner.Text();
			if filter != "" && !regex.MatchString(line) {
				continue
			}
			
			parts := strings.SplitN(line, " ", 2);
			if len(parts) < 2 {
				continue
			}
			
			firstPart := parts[0]
			if strings.Contains(firstPart, "[") && strings.Contains(firstPart, "]") {
				severity := strings.ReplaceAll(firstPart, "[", "");
				severity = strings.ReplaceAll(severity, "]", "");
				severityCounts[severity]++;
			}
		}

		if err := scanner.Err(); err != nil {
			return nil, err
		}
		return severityCounts, nil;
	}


	func main() {
		if len(os.Args) < 2 || len(os.Args) > 3 {
			fmt.Println("Usage: go run main.go <filename> [filter]");
			os.Exit(1);
		}
		
		filename := os.Args[1];
		var filter string;
		if len(os.Args) == 3 {
			filter = os.Args[2];
		}

		severityCounts, err := analyzeLogs(filename, filter);
		if err != nil {
			fmt.Fprintf(os.Stderr, "Error analyzing logs %v\n", err);
			os.Exit(1);
		}

		fmt.Println("Log Analysis Results");
		for severity, count := range severityCounts {
			fmt.Printf("%s: %d\n", severity, count);
		}
	}
    ```

	*   `analyzeLogs` reads a file line by line and counts the occurrences of each severity based on the log output format, and also has the ability to filter based on a regular expression.
	* The main program parses the command line arguments, calls the log analysis function, and prints the results.

*   **How to Run:**

    1.  Save the code above in `main.go`.
    2.  Create a log file called `app.log` and add log lines in the format `[severity] message`.
    3.  Open a terminal, navigate to the directory containing `main.go` and `app.log`, and run:

        ```bash
        go run . app.log
        ```

		or with a filter

		```bash
		go run . app.log "error"
		```
        Replace `app.log` with the path to your log file, and optionally use the second argument for a filter.

**12.2 Task Queues**

Task queues enable you to offload and manage background tasks, ensuring that time-consuming operations don't block the main program flow. Go's concurrency features make it well-suited for building efficient task queue systems.

*   **Core Functionality:**
    *   Enqueue tasks with a priority.
    *   Process tasks concurrently using multiple workers.
    *   Handle task failures or retries.
*   **Implementation:**
	```go
    // main.go
	package main

	import (
		"fmt"
		"sync"
		"time"
		"math/rand"
	)
	
	type Task struct {
		ID int
		Priority int
		Data string
	}

	var jobs = make(chan Task, 100);

	func worker(id int, wg *sync.WaitGroup) {
		defer wg.Done()
		for job := range jobs {
			fmt.Println("worker: ", id, "processing task ", job.ID, "priority", job.Priority);
			time.Sleep(time.Duration(rand.Intn(500)) * time.Millisecond); // simulate processing
		}
		fmt.Println("worker: ", id, "finished");
	}

    func main() {
        const numWorkers = 5;
		const numTasks = 10;

        var wg sync.WaitGroup
	
        for i := 0; i < numWorkers; i++ {
			wg.Add(1);
            go worker(i, &wg)
        }
		
		for i := 0; i < numTasks; i++ {
			jobs <- Task {
				ID: i + 1,
				Priority: rand.Intn(10),
				Data: fmt.Sprintf("data %d", i),
			}
		}

		close(jobs) // signal to the workers that there are no more jobs coming
        wg.Wait()
		fmt.Println("All workers have finished")
    }
	```

	*   The program defines a `Task` struct for the jobs.
	*  The `worker` function is called as a goroutine for each worker. It receives a job from the `jobs` channel and process it.
	*  The main program will create multiple workers and send 10 jobs to the queue.
	* Uses a `WaitGroup` to make sure that all the workers have completed their work.

*   **How to Run:**

    1.  Save the code above in `main.go`.
    2.  Open a terminal, navigate to the directory containing `main.go`, and run:

        ```bash
        go run .
        ```

**12.3 System Monitoring**

System monitoring tools collect and report data about system resources, performance, and health. Go's ability to access system information and its concurrency capabilities make it useful for building monitoring agents.

*   **Core Functionality:**
    *   Collect system metrics (CPU, memory, disk, network usage).
    *   Expose metrics via an API or other means.
    *   Set up alerts based on resource usage.
*   **Implementation:**

    ```go
	// main.go
	package main

	import (
		"fmt"
		"log"
		"net/http"
		"runtime"
		"time"
		"encoding/json"
	)

	type Metrics struct {
		CPUUsage float64 `json:"cpu_usage"`
		MemoryUsage uint64 `json:"memory_usage"`
		GoroutineCount int `json:"goroutine_count"`
	}
	
	func collectMetrics() Metrics {
		var memStats runtime.MemStats;
		runtime.ReadMemStats(&memStats);
		
		var cpuUsage float64 = 0;
		
		
		return Metrics{
			CPUUsage: cpuUsage, // cpu usage requires third party libraries
			MemoryUsage: memStats.Alloc,
			GoroutineCount: runtime.NumGoroutine(),
		}
	}
	
	func metricsHandler(w http.ResponseWriter, r *http.Request) {
		metrics := collectMetrics();
		w.Header().Set("Content-Type", "application/json")
		
		data, err := json.MarshalIndent(metrics, "", "    ");
		if err != nil {
			http.Error(w, "Error", http.StatusInternalServerError)
			return;
		}

		w.Write(data)
	}
	
	func main() {
		http.HandleFunc("/metrics", metricsHandler);
		fmt.Println("Server is starting at port 8080")
		log.Fatal(http.ListenAndServe(":8080", nil))
	}
    ```
	* The `Metrics` struct defines the fields we need to monitor.
    *  The `collectMetrics` collects memory and goroutines stats and adds them to the `Metrics` struct.
    *   The `metricsHandler` collects the metrics, converts them into json, and sends them to the client.
    *  This server is running at port 8080 and has an endpoint `/metrics` that you can use to check the state of the metrics.

*   **How to Run:**

    1.  Save the code above in `main.go`.
    2.  Open a terminal, navigate to the directory containing `main.go`, and run:

        ```bash
        go run .
        ```

    3.  Open a web browser and visit `http://localhost:8080/metrics` to see the collected metrics as json.

**12.4 Key Takeaways**

This chapter has explored other application areas where Go can be used:

*   **Log Analysis:** Using Go to build efficient log parsing tools and filtering using regular expressions.
*   **Task Queues:** How to implement task queues using Go's concurrency primitives.
*  **System Monitoring:** How to collect system metrics and expose them via an API.

These examples showcase the diversity of applications that can be built with Go, expanding your knowledge of what you can achieve with Go.

