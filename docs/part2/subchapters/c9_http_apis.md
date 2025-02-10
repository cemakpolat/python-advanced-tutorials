---
title: Working with HTTP and APIs
parent: Advanced Go Development
nav_order: 7
---

## Chapter 9: Working with HTTP and APIs

Go's standard library provides excellent support for building HTTP servers and clients. This chapter will introduce you to the fundamental concepts of web development with Go, allowing you to build web applications and interact with external APIs.

**9.1 Creating Simple HTTP Servers**

Go makes it easy to create HTTP servers using the `net/http` package.

*   **Basic HTTP Server:**
    ```go
    package main

    import (
        "fmt"
        "net/http"
    )

    func helloHandler(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, World!")
    }

    func main() {
        http.HandleFunc("/", helloHandler)
        fmt.Println("Server is starting at port 8080")
        http.ListenAndServe(":8080", nil)
    }
    ```
    *   `http.HandleFunc` registers a handler function for the root path (`/`).
    *   `helloHandler` is a handler function that writes "Hello, World!" to the response.
    *   `http.ListenAndServe` starts the server and listens on port 8080.

*   **Handling Different Paths:**
    ```go
    package main

    import (
        "fmt"
        "net/http"
    )

    func homeHandler(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Welcome to the home page!")
    }
    func aboutHandler(w http.ResponseWriter, r *http.Request) {
	    fmt.Fprintf(w, "This is the about page");
    }

    func main() {
        http.HandleFunc("/", homeHandler)
	http.HandleFunc("/about", aboutHandler)
        fmt.Println("Server is starting at port 8080")
        http.ListenAndServe(":8080", nil)
    }
    ```
	*   We can define new handler functions for different paths, and define what we want the server to respond.

**9.2 Handling Requests and Responses**

Understanding how to work with requests and responses is crucial for building interactive web applications.

*   **Accessing Request Data:**
    ```go
    package main

    import (
        "fmt"
        "net/http"
    )

    func requestHandler(w http.ResponseWriter, r *http.Request) {
        fmt.Println("Method:", r.Method)
        fmt.Println("URL:", r.URL.Path)
        fmt.Println("Header:", r.Header)
        fmt.Println("Query Parameters:", r.URL.Query())

        fmt.Fprintf(w, "Request processed, check console")
    }

    func main() {
	    http.HandleFunc("/", requestHandler)
        fmt.Println("Server is starting at port 8080")
        http.ListenAndServe(":8080", nil)
    }
    ```
	*   The `http.Request` struct contains a lot of information about the request that was made, such as the method, url, headers and query parameters.

*   **Sending Responses:**
    We can set the status code and headers of the response.

    ```go
    package main

    import (
        "fmt"
        "net/http"
    )

    func responseHandler(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "text/plain")
        w.WriteHeader(http.StatusOK)
        fmt.Fprintf(w, "This is a custom response!")
    }

    func main() {
	   http.HandleFunc("/", responseHandler)
       fmt.Println("Server is starting at port 8080")
       http.ListenAndServe(":8080", nil)
    }
    ```
	*   `w.Header().Set` sets the header of the response.
    *   `w.WriteHeader` sets the status code.

**9.3 Making HTTP Requests to APIs**

Go provides `net/http` for making requests to external APIs.

*   **Making a GET Request:**

    ```go
    package main

    import (
        "fmt"
        "io"
        "net/http"
        "log"
    )

    func main() {
	    url := "https://jsonplaceholder.typicode.com/todos/1";

	    resp, err := http.Get(url);
	    if err != nil {
		    log.Fatal("error making request:", err);
	    }
	    defer resp.Body.Close()

	    if resp.StatusCode != http.StatusOK {
		    log.Fatal("error non 200 status code:", resp.StatusCode);
	    }

	    data, err := io.ReadAll(resp.Body);
	    if err != nil {
		    log.Fatal("error reading response body:", err);
	    }

	    fmt.Println("response body: ", string(data))
    }
    ```
	*   `http.Get` sends a GET request to a specified URL.
	*   The response status code must be checked.
	* The response data can be read from the response body.

*   **Making a POST Request:**

    ```go
    package main

    import (
        "bytes"
        "encoding/json"
        "fmt"
        "log"
        "net/http"
	    "io"
    )

    type Post struct {
        UserID  int    `json:"userId"`
        Title string `json:"title"`
        Body    string `json:"body"`
    }

    func main() {
        post := Post{
            UserID:  1,
            Title: "New post",
            Body: "This is a new post created by my application",
        }

        jsonBody, err := json.Marshal(post)
        if err != nil {
		    log.Fatal("Error marshalling json", err);
	    }

        resp, err := http.Post("https://jsonplaceholder.typicode.com/posts", "application/json", bytes.NewBuffer(jsonBody))
        if err != nil {
		    log.Fatal("error making request:", err);
        }
        defer resp.Body.Close()
        if resp.StatusCode != http.StatusCreated {
            log.Fatal("error response code", resp.StatusCode);
        }
        data, err := io.ReadAll(resp.Body);
        if err != nil {
            log.Fatal("Error reading body", err);
        }

        fmt.Println("Response data:", string(data));
    }
    ```
	*   `http.Post` makes a POST request to the specified URL.
	*   We marshall the go struct to a json to be sent to the api.
	*   The response data can be read from the response body.
	* The response status code must be checked.

**9.4 Parsing JSON and XML Data**

Go provides packages for parsing JSON and XML data.

*   **Parsing JSON Data:**

    ```go
    package main

    import (
        "encoding/json"
        "fmt"
        "log"
        "net/http"
        "io"
    )

    type Todo struct {
        UserID    int    `json:"userId"`
        ID        int    `json:"id"`
        Title     string `json:"title"`
        Completed bool   `json:"completed"`
    }

    func main() {
        resp, err := http.Get("https://jsonplaceholder.typicode.com/todos/1")
        if err != nil {
           log.Fatal("Error making request", err);
        }
        defer resp.Body.Close()
	    if resp.StatusCode != http.StatusOK {
		    log.Fatal("Error, non 200 code received", resp.StatusCode)
	    }
        data, err := io.ReadAll(resp.Body);
	    if err != nil {
		    log.Fatal("Error reading response body:", err);
	    }


	    var todo Todo
        err = json.Unmarshal(data, &todo)
        if err != nil {
           log.Fatal("Error unmarshalling json", err);
        }

        fmt.Println("Todo title:", todo.Title)
	    fmt.Println("Todo completed:", todo.Completed);
    }
    ```
    *   `json.Unmarshal` parses JSON data and populates the struct.

*   **Parsing XML Data:**

    ```go
    package main

    import (
        "encoding/xml"
        "fmt"
        "io"
        "log"
        "net/http"
    )

    type Item struct {
        Title string `xml:"title"`
        Link  string `xml:"link"`
    }

    type Channel struct {
        Items []Item `xml:"item"`
    }

    type Rss struct {
        Channel Channel `xml:"channel"`
    }

    func main() {
	    url := "https://www.npr.org/rss/podcast.php?id=510298"; // NPR Technology Podcast RSS Feed

	    resp, err := http.Get(url)
	    if err != nil {
		    log.Fatal("error making request", err)
	    }
	    defer resp.Body.Close()

	    if resp.StatusCode != http.StatusOK {
		log.Fatal("Error: received non 200 status code", resp.StatusCode)
	    }

	    data, err := io.ReadAll(resp.Body);
	    if err != nil {
		    log.Fatal("Error reading response body:", err)
	    }
	    var rss Rss;
	    err = xml.Unmarshal(data, &rss)
	    if err != nil {
		    log.Fatal("Error unmarshalling xml:", err)
	    }

	    for _, item := range rss.Channel.Items {
		    fmt.Println("Title:", item.Title)
		    fmt.Println("Link:", item.Link);
		    fmt.Println("---")
	    }
    }
    ```
    *  `xml.Unmarshal` parses XML data into a struct based on the xml tags.

**9.5 Key Takeaways**

This chapter has covered the fundamentals of working with HTTP and APIs in Go:

*   **Creating HTTP Servers:** Use `net/http` to create basic servers, handle different paths, and customize responses.
*   **Request and Response:**  `http.Request` contains info about the request, and `http.ResponseWriter` allows you to handle and customize the response.
*   **Making HTTP Requests:** Use `http.Get` and `http.Post` to interact with external APIs.
*   **Parsing JSON and XML:** Use `encoding/json` and `encoding/xml` to handle different data formats.

Understanding these concepts allows you to build powerful web applications and integrate them with external services.

