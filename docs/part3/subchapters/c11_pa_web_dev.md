---
title: Web Development
parent: Real-World Applications
nav_order: 2
---

## Chapter 11: Practical Application: Web Development

Go's strong support for concurrency and its efficient standard library make it a great choice for web development. This chapter will guide you through building web applications and clients, using Go's web capabilities.

**11.1 Example: A Basic Web Server with Routing**

This example will demonstrate how to create a basic web server that handles different routes and responses.

*   **Core Functionality:**
    *   Handle HTTP requests on different paths.
    *   Respond with different content based on the path.
	*   Handle query parameters.
	*   Handle POST methods.
*   **Implementation:**

    ```go
	// main.go
	package main

    import (
        "fmt"
        "log"
        "net/http"
        "encoding/json"
        "io"
    )

	type Post struct {
		Message string `json:"message"`
		Author string `json:"author"`
	}
	func homeHandler(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Welcome to the Home page!")
	}
	
	func aboutHandler(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "This is the about page!")
	}
	
	func postsHandler(w http.ResponseWriter, r *http.Request) {
	    fmt.Println("Query parameters:", r.URL.Query());
		switch r.Method {
			case http.MethodGet:
				fmt.Fprintf(w, "This is a list of posts!");
			case http.MethodPost:
				body, err := io.ReadAll(r.Body);
				if err != nil {
					http.Error(w, "Error reading the body", http.StatusBadRequest);
					return;
				}
				var post Post;

				err = json.Unmarshal(body, &post);

				if err != nil {
					http.Error(w, "Error unmarshalling json", http.StatusBadRequest);
					return;
				}
				fmt.Fprintf(w, "Post created:\n");
				fmt.Fprintf(w, "Message: %s\nAuthor: %s\n", post.Message, post.Author);
			default:
				http.Error(w, "Method not allowed", http.StatusMethodNotAllowed);
		}
	}
	
    func main() {
		http.HandleFunc("/", homeHandler);
		http.HandleFunc("/about", aboutHandler);
		http.HandleFunc("/posts", postsHandler);
		
		fmt.Println("Server is starting at port 8080")
		log.Fatal(http.ListenAndServe(":8080", nil))
	}
    ```
	*   The program defines handlers for `/` for the home page, `/about` for the about page and `/posts` for viewing and creating posts.
	*   The program handles query parameters in the `/posts` endpoint.
	*   The program can accept POST methods in `/posts` endpoint by sending a json with the properties of `Message` and `Author`.
	*   The program will return http errors for common failures.
*   **How to Run:**

    1.  Save the code above in a `main.go` file.
    2.  Open a terminal, navigate to the directory containing `main.go`, and run:

        ```bash
        go run .
        ```

    3.  Open a web browser and visit `http://localhost:8080`, `http://localhost:8080/about`, `http://localhost:8080/posts`, and `http://localhost:8080/posts?param1=value1&param2=value2`. Also use tools like `curl` or `Postman` to send a POST request to `http://localhost:8080/posts` using a body in the json format `{"message": "This is a new message", "author": "John"}`.

**11.2 Example: A REST API Client**

This example will demonstrate how to create a command-line tool that interacts with a REST API, retrieves data and prints to the console.

*   **Core Functionality:**
    *   Accept a URL as input for a REST API.
    *   Make a GET request.
    *   Parse JSON data.
    *   Display the data.

*   **Implementation:**
    ```go
    // main.go
	package main

	import (
		"encoding/json"
		"fmt"
		"io"
		"log"
		"net/http"
		"os"
	)

	type Todo struct {
		UserID    int    `json:"userId"`
		ID        int    `json:"id"`
		Title     string `json:"title"`
		Completed bool   `json:"completed"`
	}
	
	func fetchAndPrintData(url string) error {
		resp, err := http.Get(url)
		if err != nil {
			return fmt.Errorf("error making request: %w", err)
		}
		defer resp.Body.Close();

		if resp.StatusCode != http.StatusOK {
			return fmt.Errorf("received non 200 status code: %d", resp.StatusCode)
		}

		data, err := io.ReadAll(resp.Body);
		if err != nil {
			return fmt.Errorf("error reading response body: %w", err);
		}

		var todo Todo;
		if err := json.Unmarshal(data, &todo); err != nil {
			return fmt.Errorf("error unmarshalling json %w", err);
		}

		fmt.Printf("Todo ID: %d\n", todo.ID)
		fmt.Printf("Title: %s\n", todo.Title)
		fmt.Printf("Completed: %t\n", todo.Completed)
		return nil;
	}

	func main() {
		if len(os.Args) != 2 {
			fmt.Println("Usage: go run main.go <url>");
			os.Exit(1);
		}
		url := os.Args[1];

		err := fetchAndPrintData(url);
		if err != nil {
			log.Fatal("error fetching data: ", err);
		}

	}
    ```
	*   The program defines a `Todo` struct that has fields with the correct json tags.
    *  The `fetchAndPrintData` makes the GET request, reads the response body, unmarshalls it, and prints the content.
    *   The program uses a command-line argument to pass the url.
    *   The program will output to standard error when something goes wrong.

*   **How to Run:**

    1.  Save the code above in a `main.go` file.
    2.  Open a terminal, navigate to the directory containing `main.go`, and run:

        ```bash
        go run . <URL>
        ```

        Replace `<URL>` with the URL of the REST API endpoint you want to interact with. For example:
		```bash
		go run . https://jsonplaceholder.typicode.com/todos/1
		```

**11.3 Example: A Simple Chat Server (TCP)**

This example will demonstrate how to create a simple TCP server that allows clients to connect and send messages to each other.

*   **Core Functionality:**
    *   Listen for TCP connections.
    *   Handle multiple clients concurrently.
    *   Broadcast messages from one client to all other clients.
*   **Implementation:**

    ```go
    // main.go
	package main

	import (
		"bufio"
		"fmt"
		"log"
		"net"
	)
	
	type Client struct {
		conn net.Conn
		writer *bufio.Writer
	}

	var clients = make(map[net.Conn]Client);
	var clientChannel = make(chan Client);
	var messageChannel = make(chan string);
	var quitChannel = make(chan net.Conn);
	
	func handleConnection(conn net.Conn) {
		client := Client {
			conn: conn,
			writer: bufio.NewWriter(conn),
		}
		clientChannel <- client;
		reader := bufio.NewReader(conn);
		for {
			message, err := reader.ReadString('\n');
			if err != nil {
				quitChannel <- conn;
				return;
			}
			messageChannel <- message
		}
	}
	
	func broadcastMessage(message string, sender net.Conn) {
		for conn, client := range clients {
			if conn != sender {
				_, err := client.writer.WriteString(message);
				if err != nil {
					log.Println("Error sending message:", err);
					quitChannel <- conn;
				}
				err = client.writer.Flush(); // flush the messages immediately.
				if err != nil {
					log.Println("Error flushing the messages:", err);
					quitChannel <- conn
				}
			}
		}
	}
	
	func manageConnections() {
		for {
			select {
				case client := <- clientChannel:
					clients[client.conn] = client
					fmt.Println("New client connected: ", client.conn.RemoteAddr());
					
				case message := <- messageChannel:
					broadcastMessage(message, clients[0].conn);
				case conn := <- quitChannel:
					fmt.Println("Client disconnected: ", conn.RemoteAddr());
					delete(clients, conn);
					conn.Close();
			}
		}
	}
	
	func main() {
		listener, err := net.Listen("tcp", ":8080");
		if err != nil {
			log.Fatal("Error creating listener: ", err);
		}
		defer listener.Close();
		
		fmt.Println("Server is listening at port 8080");
		
		go manageConnections()
		for {
			conn, err := listener.Accept();
			if err != nil {
				log.Println("error accepting connection: ", err);
				continue;
			}
			go handleConnection(conn)
		}
	}
    ```

	*   The server creates a TCP listener at port 8080.
	*   `manageConnections` function handles new connections and sends messages to other clients.
	*   `handleConnection` is started as a goroutine for every new connection. It reads the client messages and sends them to the `messageChannel`.
	*   The server uses channels to handle incoming connections, messages and disconnections.
	*  The messages are broadcasted to all clients but the sender.
*   **How to Run:**

    1.  Save the code above in `main.go`.
    2.  Open a terminal, navigate to the directory containing `main.go`, and run:

        ```bash
        go run .
        ```

    3.  Use multiple `netcat` clients or a similar tool to connect to `localhost:8080`, send messages and see them broadcasted in the other clients.

**11.4 Key Takeaways**

This chapter has provided practical examples of web development using Go:

*   **Web Server with Routing:** Using `net/http` to create web servers and handle different routes and methods.
*   **REST API Client:** Using Go to make requests to external APIs, handling the response and displaying data.
*   **TCP Chat Server:** Demonstrates how to build a TCP server, handle multiple clients using goroutines and channels and how to broadcast messages to different clients.

These examples provide a foundation for building a wide variety of web applications and network services using Go.

