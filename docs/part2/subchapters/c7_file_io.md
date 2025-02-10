---
title: File Handling and Input/Output
parent: Advanced Go Development
nav_order: 5
---

## Chapter 7: File Handling and Input/Output

Efficient input and output operations are essential for any software application. Go provides robust packages for interacting with files, handling various data formats, and managing standard I/O streams. This chapter will guide you through the most common I/O tasks.

**7.1 Reading From and Writing To Files**

Go provides several ways to interact with files.

*   **Opening and Closing Files:**

    ```go
    package main

    import (
        "fmt"
        "os"
    )

    func main() {
        // Opening a file in read mode
        file, err := os.Open("myfile.txt")
        if err != nil {
            fmt.Println("Error opening file:", err)
            return
        }
        defer file.Close() // ensure file is closed after it has been used
        
        fmt.Println("File opened successfully!");

        // Opening a file in write mode
        writeFile, err := os.Create("newfile.txt");
        if err != nil {
            fmt.Println("Error creating file", err);
            return;
        }
        defer writeFile.Close()
        fmt.Println("File created successfully");

        writeFile, err = os.OpenFile("existingfile.txt", os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0644)
        if err != nil {
            fmt.Println("Error opening existing file:", err);
            return
        }
        
        defer writeFile.Close();
            fmt.Println("File opened with append mode.");
        }
    ```

    * `os.Open` opens an existing file for reading.
    * `os.Create` creates a new file for writing.
	* `os.OpenFile` opens a file with specified mode and permissions.
	* You can use `os.O_APPEND` to add to the file instead of overriding. Use `os.O_CREATE` to create if it does not exist.
	*   Always defer closing the file to avoid resource leaks.

*   **Reading from Files:**

    ```go
    package main

    import (
        "fmt"
        "io"
        "os"
    )

    func main() {
        file, err := os.Open("myfile.txt")
        if err != nil {
            fmt.Println("Error opening file:", err)
            return
        }
        defer file.Close()

	// read the entire file at once
        data, err := io.ReadAll(file)
        if err != nil {
            fmt.Println("Error reading file:", err)
            return
        }
        fmt.Println("File contents:\n", string(data))
    }
    ```
     *  `io.ReadAll` reads the entire content of the file and returns it as a byte slice, or returns an error if one occurs.

*   **Writing to Files:**

    ```go
    package main

    import (
        "fmt"
        "os"
    )

    func main() {
        file, err := os.Create("output.txt")
        if err != nil {
            fmt.Println("Error creating file:", err)
            return
        }
        defer file.Close()

        _, err = file.WriteString("This is some text written to the file.\n")
        if err != nil {
            fmt.Println("Error writing to file:", err)
            return
        }
	
        data := []byte("More text to be added\n")
        _, err = file.Write(data);
        if err != nil {
            fmt.Println("Error writing byte array to file:", err);
            return
        }


        fmt.Println("Data written to file successfully!")
    }
    ```
    *   `file.WriteString` writes a string to a file, and returns an error if any happened.
    *   `file.Write` writes a byte array to a file.

**7.2 Using `bufio` for Efficient I/O**

The `bufio` package provides buffered I/O operations, which can significantly improve performance when reading or writing large amounts of data.

*   **Reading with `bufio.Scanner`:**

    ```go
    package main

    import (
        "bufio"
        "fmt"
        "os"
    )

    func main() {
        file, err := os.Open("myfile.txt")
        if err != nil {
            fmt.Println("Error opening file:", err)
            return
        }
        defer file.Close()

        scanner := bufio.NewScanner(file)
        for scanner.Scan() {
            fmt.Println("Line:", scanner.Text())
        }

        if err := scanner.Err(); err != nil {
            fmt.Println("Error reading file:", err)
        }
    }
    ```
    *   `bufio.NewScanner` creates a new scanner for reading line by line from the file.
    *   `scanner.Scan()` reads the next line and returns true if it has data.
    *   `scanner.Text()` returns the current line as string.
    *   `scanner.Err()` returns the error (if any).

*   **Writing with `bufio.Writer`:**

    ```go
    package main

    import (
        "bufio"
        "fmt"
        "os"
    )

    func main() {
        file, err := os.Create("buffered_output.txt")
        if err != nil {
            fmt.Println("Error creating file:", err)
            return
        }
        defer file.Close()

        writer := bufio.NewWriter(file)
        _, err = writer.WriteString("This is a line written to file using bufio\n");
        if err != nil {
            fmt.Println("error writing to file:", err);
            return
        }
        
            _, err = writer.WriteString("Another line written to the file using bufio\n");
        if err != nil {
            fmt.Println("error writing to file:", err);
            return
        }

            err = writer.Flush(); // flush to make sure all data was written
        if err != nil {
            fmt.Println("error flushing data:", err);
            return
        }

        fmt.Println("Data written to file sucessfully");
    }
    ```
    *   `bufio.NewWriter` creates a new writer, which allows you to write buffered data.
    *   `writer.WriteString()` writes to buffer instead of directly to the file.
    *   `writer.Flush()` is used to flush any buffered data to the file.

**7.3 Working with Different Formats (CSV, JSON, etc.)**

Go provides standard libraries for handling common data formats.

*   **CSV (Comma-Separated Values):**

    ```go
    package main

    import (
        "encoding/csv"
        "fmt"
        "os"
    )

    func main() {
        file, err := os.Open("data.csv")
        if err != nil {
            fmt.Println("Error opening file:", err)
            return
        }
        defer file.Close()

        reader := csv.NewReader(file)
        records, err := reader.ReadAll()
        if err != nil {
            fmt.Println("Error reading CSV:", err)
            return
        }

        for _, record := range records {
            fmt.Println(record)
        }
    }
    ```
    *   `csv.NewReader` reads data in csv format.
    *   `reader.ReadAll` reads the entire file into `[][]string`.

*   **JSON (JavaScript Object Notation):**
     ```go
	package main

    import (
        "encoding/json"
        "fmt"
        "os"
	"io"
    )

	type Person struct {
		Name string `json:"name"`
		Age int `json:"age"`
		Hobbies []string `json:"hobbies"`
	}
    func main() {
	   file, err := os.Open("data.json");
	   if err != nil {
		   fmt.Println("error opening file:", err);
		   return;
	   }

	   defer file.Close();
	   
	   content, err := io.ReadAll(file);
	   if err != nil {
		   fmt.Println("Error reading json file:", err);
		   return;
	   }
	   var person Person;

	   err = json.Unmarshal(content, &person);
	    if err != nil {
		    fmt.Println("Error unmarshalling data:", err);
		    return
	    }

	    fmt.Println(person);
    }
     ```
    *   `json.Unmarshal` parses JSON data into a Go struct.
	*   Tags such as `json:"name"` are used to specify how to map json fields into the corresponding fields of the struct.
    ```go
    package main

    import (
	    "encoding/json"
	    "fmt"
	    "os"
	    "log"
    )
    type Person struct {
	    Name string `json:"name"`
	    Age  int    `json:"age"`
	    Hobbies []string `json:"hobbies"`
    }
    func main() {
	    person := Person{
		    Name: "John Doe",
		    Age:  30,
		    Hobbies: []string{"reading", "hiking"},
	    }
	    
	    data, err := json.MarshalIndent(person, "", "  ");
	    if err != nil {
		    log.Fatal("Error marshalling json:", err);
	    }
	    
	    file, err := os.Create("output.json");
	    if err != nil {
		    log.Fatal("Error creating file:", err);
	    }
	    defer file.Close();

	    _, err = file.Write(data);
	    if err != nil {
		    log.Fatal("Error writing to file:", err);
	    }

	    fmt.Println("Data written to json file successfully");
    }
    ```
    *   `json.Marshal` converts a struct into a json.
	* `json.MarshalIndent` marshalls a struct with indents to improve readability.

**7.4 Working with Standard Input/Output**

Go provides access to standard input, output, and error streams.

*   **Standard Input:**

    ```go
    package main

    import (
        "bufio"
        "fmt"
        "os"
    )

    func main() {
        reader := bufio.NewReader(os.Stdin)
        fmt.Print("Enter text: ")
        input, _ := reader.ReadString('\n')
        fmt.Println("You entered:", input)
    }
    ```
    *   `bufio.NewReader(os.Stdin)` creates a new reader for standard input.
    *   `reader.ReadString('\n')` reads input until a newline character.

*   **Standard Output:**
    We have been using `fmt.Println` all over our examples. This function writes to standard output.

* **Standard Error:**
	We can output errors to standard error by using `fmt.Fprintln`:
	```go
    package main

    import (
        "fmt"
        "os"
    )

    func main() {
        fmt.Fprintln(os.Stderr, "This is an error message to standard error!");
    }
    ```

**7.5 Key Takeaways**

This chapter has explored file handling and input/output operations in Go:

*   **File I/O:** Use `os.Open`, `os.Create`, `os.OpenFile` to open files, `defer file.Close()` to make sure files are closed, `io.ReadAll` to read all the content of a file, `file.WriteString()` to write strings, and `file.Write()` to write byte slices.
*   **Efficient I/O with `bufio`:** Use `bufio.Scanner` to read files line by line, `bufio.Writer` for buffered writing, and remember to use `Flush()` to make sure everything is written.
*   **Working with Data Formats:** Use `encoding/csv` for CSV, and `encoding/json` for JSON.
*   **Standard I/O:** Use `bufio.NewReader(os.Stdin)` to interact with standard input, use `fmt.Println` to print to standard output, and use `fmt.Fprintln(os.Stderr, ...)` to print to standard error.

Understanding these concepts is crucial for building applications that interact with data in various forms.

