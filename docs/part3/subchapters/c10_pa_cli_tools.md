---
title: Building CLI Tools
parent: Real-World Applications
nav_order: 1
---


## Chapter 10: Practical Application: Building CLI Tools

Command-line tools are essential for automating tasks and interacting with systems. Go's efficiency and simplicity make it a great language for creating powerful CLI applications. In this chapter, we'll develop three practical examples that demonstrate how to use Go to build useful CLI tools.

**10.1 Example: A Simple To-Do List Manager**

This example will demonstrate how to create a basic to-do list manager, allowing users to add, list, and mark tasks as complete.

*   **Core Functionality:**
    *   Add a new task
    *   List all tasks
    *   Mark tasks as complete
    *   Data persistence via a text file.

*   **Implementation:**
    ```go
	// main.go
	package main

	import (
		"bufio"
		"fmt"
		"os"
		"strings"
		"strconv"
	)

	type Task struct {
		ID int
		Description string
		Completed bool
	}
	
	func addTask(tasks *[]Task, description string) {
		newTask := Task {
			ID: len(*tasks) + 1,
			Description: description,
			Completed: false,
		}
		*tasks = append(*tasks, newTask);
		fmt.Println("Task added!");
		saveTasksToFile(*tasks, "tasks.txt");
	}

	func listTasks(tasks []Task) {
		if len(tasks) == 0 {
			fmt.Println("No tasks found")
			return
		}
		for _, task := range tasks {
			status := "[ ]"
			if task.Completed {
				status = "[x]"
			}
			fmt.Printf("%d %s %s\n", task.ID, status, task.Description)
		}
	}

	func completeTask(tasks *[]Task, id int) {
		for i, task := range *tasks {
			if task.ID == id {
				(*tasks)[i].Completed = true
				fmt.Println("Task marked as complete")
				saveTasksToFile(*tasks, "tasks.txt");
				return
			}
		}
		fmt.Println("Task not found")
	}

	func loadTasksFromFile(filename string) ([]Task, error) {
		file, err := os.Open(filename)
		if err != nil {
			if os.IsNotExist(err) {
				return []Task{}, nil;
			}
			return nil, err;
		}

		defer file.Close();
		var tasks []Task;

		scanner := bufio.NewScanner(file);
		for scanner.Scan() {
			line := scanner.Text();
			var task Task;
			_, err = fmt.Sscan(line, &task.ID, &task.Description, &task.Completed);
			if err != nil {
				// skip invalid lines
				continue
			}
			tasks = append(tasks, task);
		}
		return tasks, nil
	}

	func saveTasksToFile(tasks []Task, filename string) error {
		file, err := os.Create(filename)
		if err != nil {
			return err;
		}
		defer file.Close();
		for _, task := range tasks {
			_, err = fmt.Fprintf(file, "%d %s %t\n", task.ID, task.Description, task.Completed)
			if err != nil {
				return err;
			}
		}
		return nil;
	}

	func main() {
		tasks, err := loadTasksFromFile("tasks.txt")
		if err != nil {
			fmt.Fprintf(os.Stderr, "Error loading tasks from file: %v\n", err);
			os.Exit(1)
		}
		
		reader := bufio.NewReader(os.Stdin)
	
		for {
			fmt.Print("Enter command (add/list/complete/exit): ");
			input, _ := reader.ReadString('\n')
			input = strings.TrimSpace(input)
		
			parts := strings.SplitN(input, " ", 2);
			command := parts[0];

			switch command {
			case "add":
				if len(parts) < 2 {
					fmt.Println("Description required!");
					continue;
				}
				addTask(&tasks, parts[1])
			case "list":
				listTasks(tasks)
			case "complete":
				if len(parts) < 2 {
					fmt.Println("Task ID required!");
					continue;
				}
				id, err := strconv.Atoi(parts[1])
				if err != nil {
					fmt.Println("Invalid task id!");
					continue;
				}
				completeTask(&tasks, id)
			case "exit":
				fmt.Println("Exiting the application");
				return
			default:
				fmt.Println("Invalid command. Please enter add/list/complete or exit");
				}
		}
	}
    ```
	*   Defines functions for adding, listing, completing tasks and persistence.
	*   Reads command input using a `bufio.Reader`.
    *   Uses `switch` statement to handle commands.
	*   Saves the tasks to a `tasks.txt` file.
	*   Loads the task from the `tasks.txt` file if one exists.
	*   The format of the `tasks.txt` file is `id description completed`.

*   **How to Run:**

    1.  Save the code above in a `main.go` file.
    2.  Open a terminal, navigate to the directory containing `main.go`, and run:

        ```bash
        go run .
        ```

    3.  Follow the prompts to manage tasks.

**10.2 Example: A Word Frequency Counter**

This example will demonstrate how to create a CLI tool that counts the frequency of each word in a text file.

*   **Core Functionality:**
    *   Read a text file
    *   Count the occurrences of each word
    *   Print the top N most frequent words

*   **Implementation:**

    ```go
    // main.go
    package main

    import (
        "bufio"
        "fmt"
        "os"
        "sort"
        "strings"
        "strconv"
    )

    func countWords(filename string) (map[string]int, error) {
        file, err := os.Open(filename)
        if err != nil {
            return nil, err
        }
        defer file.Close()

        wordCounts := make(map[string]int)
        scanner := bufio.NewScanner(file)
        scanner.Split(bufio.ScanWords)

        for scanner.Scan() {
            word := strings.ToLower(scanner.Text())
            wordCounts[word]++
        }

		if err = scanner.Err(); err != nil {
			return nil, err;
		}
        return wordCounts, nil
    }

    func printTopWords(wordCounts map[string]int, topN int) {
        type kv struct {
            key   string
            value int
        }

        var ss []kv
        for k, v := range wordCounts {
            ss = append(ss, kv{k, v})
        }

        sort.Slice(ss, func(i, j int) bool {
            return ss[i].value > ss[j].value
        })
		
		fmt.Println("Top words:");
        for i := 0; i < topN && i < len(ss); i++ {
			fmt.Printf("%s: %d\n", ss[i].key, ss[i].value);
        }
    }

    func main() {
        if len(os.Args) != 3 {
			fmt.Println("Usage: go run main.go <filename> <topN>")
			os.Exit(1);
        }
        filename := os.Args[1];
		topN, err := strconv.Atoi(os.Args[2])
		if err != nil {
			fmt.Println("Invalid topN value!");
			os.Exit(1);
		}

        wordCounts, err := countWords(filename)
        if err != nil {
            fmt.Fprintf(os.Stderr, "Error counting words: %v\n", err)
            os.Exit(1)
        }

        printTopWords(wordCounts, topN)
    }
    ```
	*   `countWords` reads the file and uses a map to count the frequency of each word, converting all words to lowercase.
	*   `printTopWords` uses a sort.Slice to sort the map by value and print the top N results.
	*   The program accepts the file name and the topN value as command line arguments.

*   **How to Run:**

    1.  Save the code above in `main.go`.
    2.  Create a text file named `mytext.txt`
    3.  Open a terminal, navigate to the directory containing `main.go` and `mytext.txt`, and run:

        ```bash
        go run . mytext.txt 5
        ```

        Replace `mytext.txt` with the name of your input file, and 5 with the desired `topN` value.

**10.3 Example: A Command-Line File Downloader**

This example will demonstrate how to create a CLI tool that downloads a file from a given URL.

*   **Core Functionality:**
    *   Accept a URL as input.
    *   Download the file from the URL.
    *   Save the file to disk.

*   **Implementation:**

    ```go
	package main

    import (
        "fmt"
        "io"
        "net/http"
        "os"
        "path/filepath"
    )

    func downloadFile(url string, filepath string) error {
		resp, err := http.Get(url)
		if err != nil {
			return err
		}
		defer resp.Body.Close()
		
		if resp.StatusCode != http.StatusOK {
			return fmt.Errorf("invalid response code %d", resp.StatusCode)
		}

		file, err := os.Create(filepath)
		if err != nil {
			return err
		}
		defer file.Close()

		_, err = io.Copy(file, resp.Body);
		return err;
	}

	func main() {
		if len(os.Args) != 3 {
		fmt.Println("Usage: go run main.go <url> <filepath>");
		os.Exit(1)
		}

		url := os.Args[1];
		filepath := os.Args[2];
		
		err := downloadFile(url, filepath)
		if err != nil {
			fmt.Fprintf(os.Stderr, "Error downloading file: %v\n", err);
			os.Exit(1)
		}
		fmt.Println("File downloaded successfully!");
    }
    ```
    *   `downloadFile` downloads the file from a URL and saves it to a specified path.
	*   The main program will validate the arguments and output to standard error if any issues are found.
	*   The program will create the file if it does not exist.

*   **How to Run:**

    1.  Save the code above in `main.go`.
    2.  Open a terminal, navigate to the directory containing `main.go`, and run:

        ```bash
        go run . <URL> <filePath>
        ```

        Replace `<URL>` with the URL of the file you want to download and `<filePath>` with the path where you want to save the downloaded file, including the file name.

**10.4 Key Takeaways**

This chapter has demonstrated the practical application of Go for building useful CLI tools:

*   **To-Do List Manager:** Demonstrates basic file handling, input processing, and data management for a simple app.
*   **Word Frequency Counter:** Shows how to read text from a file, use maps for counting, sort data, and output results.
*   **Command-Line File Downloader:** Shows how to make HTTP requests, handle file I/O for downloading files and handle command-line arguments.

These examples provide a solid foundation for you to explore more complex CLI applications with Go.

