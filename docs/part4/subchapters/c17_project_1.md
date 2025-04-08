---
title: Advanced Data Structures and Algorithms
parent: Applying Advanced Concepts
nav_order: 1
---

## Chapter 17: Project 1 - Building a Scalable Web Scraping and Data Aggregation System

*   **17.1 Project Overview and Requirements:**

    This project aims to construct a robust and scalable system for web scraping and data aggregation. Our goal isn't just to extract data, but to build a production-ready solution that can handle the challenges of real-world web scraping, including varying website structures, rate limiting, error handling, and data consistency.

    **Scope:**

    *   **Target Websites:** We'll focus on scraping data from e-commerce websites (e.g., Amazon, eBay) to collect product information (name, price, description, images). The system should be designed to be easily adaptable to other types of websites with minimal code changes.
    *   **Data Extraction:** The core data points to extract will be product name, current price, a short description, the image URL (if available), and ideally some key product specifications.
    *   **Performance and Scalability:** The system should be able to scrape data from multiple websites concurrently and process a large number of products efficiently. We'll aim for a design that can be scaled horizontally by adding more workers to the task queue.
    *   **Data Store:** We'll use PostgreSQL as our data store due to its reliability, support for complex queries, and excellent asynchronous support.
    *   **Error Handling and Retries:** We need robust error handling to deal with network issues, website changes, and other unexpected problems. The system should automatically retry failed tasks with exponential backoff.
    *   **Monitoring:** Implement a basic monitoring system to track the number of scraped products, the number of errors, and the overall system performance.

    **Ethical Considerations:**

    As responsible developers, we must adhere to ethical web scraping practices:

    *   **Respect `robots.txt`:** Always check the `robots.txt` file of the target website to determine which pages are allowed to be scraped.
    *   **Avoid Excessive Scraping:** Implement rate limiting to avoid overloading the target websites and potentially causing them to crash.
    *   **User-Agent:** Set a descriptive User-Agent header in your HTTP requests to identify your scraper.
    *   **Terms of Service:** Review the website's terms of service to ensure that web scraping is permitted.

    **Data Model:**

    We'll define a Pydantic model to represent the scraped product data:

    ```python
    from pydantic import BaseModel, HttpUrl
    from typing import Optional

    class Product(BaseModel):
        name: str
        price: float
        description: Optional[str] = None
        image_url: Optional[HttpUrl] = None
        specifications: Optional[dict] = None # For storing product specs
        source_url: HttpUrl # URL of the product page
    ```

    This model will serve as the foundation for data validation and ensure data consistency throughout the system.

*   **17.2 Core Components:**

    Let's outline the key components of our scalable web scraping and data aggregation system. We'll use a modular design, making it easier to test, maintain, and extend the system.

    *   **Web Scraping Module:**

        *   **Technology:** `aiohttp` (asynchronous HTTP client), `beautifulsoup4` (HTML parsing).
        *   **Responsibilities:**
            *   Fetches HTML content from target websites asynchronously.
            *   Parses the HTML to extract product data.
            *   Implements rate limiting and error handling.
        *   **Key Classes/Functions:**
            *   `AsyncWebScraper`: Base class for website-specific scrapers.
            *   `AmazonProductScraper`: Subclass of `AsyncWebScraper` for scraping Amazon.
            *   `scrape_product(url)`: Asynchronous function to scrape a single product page.

    *   **Data Cleaning and Transformation Module:**

        *   **Technology:** Regular expressions, string manipulation functions, custom data validation functions.
        *   **Responsibilities:**
            *   Cleans and transforms the extracted data to conform to the `Product` data model.
            *   Handles missing values and inconsistent data formats.
        *   **Key Classes/Functions:**
            *   `DataCleaner`: Base class for data cleaning and transformation.
            *   `clean_product_data(product_data)`: Function to clean and validate product data.

    *   **Data Aggregation and Storage Module:**

        *   **Technology:** PostgreSQL (as the data store), `asyncpg` (asynchronous PostgreSQL driver).
        *   **Responsibilities:**
            *   Stores the cleaned and transformed data in the PostgreSQL database.
            *   Provides functions for querying and retrieving data from the database.
        *   **Key Classes/Functions:**
            *   `PostgreSQLClient`: Class for managing the PostgreSQL connection pool.
            *   `insert_product(product_data)`: Asynchronous function to insert product data into the database.

    *   **Task Management and Monitoring Module:**

        *   **Technology:** Celery (as the task queue), Redis (as the broker), Prometheus (for metrics), Grafana (for visualization), Logging module.
        *   **Responsibilities:**
            *   Queues and manages the scraping and processing tasks.
            *   Monitors the progress of the tasks.
            *   Handles task retries and error reporting.
            *   Provides metrics for monitoring system performance.
        *   **Key Classes/Functions:**
            *   `celery_app`: Celery application instance.
            *   `scrape_and_process_product(url)`: Celery task to scrape and process a product.
            *   `monitor_system_status()`

    *   **Configuration Management:**

        *   Responsabilities:
            *   All setup and configuration should come from Operating System or docker file configuration
            *   Do not push confidential things to github
            *   Avoid harcoded things and use standard config files
            *   Have your files well documented

*   **17.3 Implementing the Web Scraping Module:**

    We'll start with the core of the system: the web scraping module. This component will be responsible for fetching the HTML content from the target websites and parsing it to extract the desired product data.
    Since we will be running different task, we will generate tasks per URL and them send them to a queue

    Here is the Agent file
    ```python
    import aiohttp
    from bs4 import BeautifulSoup
    import asyncio
    import logging
    from urllib.parse import urljoin

    class AsyncWebScraper:
        """
        Base class for asynchronous web scrapers.
        """
        def __init__(self, base_url, rate_limit=2):
            self.base_url = base_url
            self.rate_limit = rate_limit
            self.semaphore = asyncio.Semaphore(rate_limit) # Limit number of users
            self.headers = {'User-Agent': 'MyScalableWebScraper/1.0'} #Important to set the user agent

        async def fetch_html(self, session, url):
            """
            Fetches the HTML content from a URL.
            """
            try:
                async with self.semaphore: #This method is only called 2 times at the same time
                    async with session.get(url, headers=self.headers) as response:
                        response.raise_for_status() # Raise HTTPError for bad responses (4xx or 5xx)
                        return await response.text()
            except aiohttp.ClientError as e:
                logging.error(f"Error fetching {url}: {e}")
                return None # Avoid response Errors to be catched on code

        async def scrape_product(self, url):
            """
            Scrapes a single product page and extracts the data.
            This method should be implemented by subclasses.
            """
            raise NotImplementedError # Force implementation, to avoid generic executions
        #Utilies
        def get_absolute_url(self, relative_url):
            """Convert relative url to absolute"""
            return urljoin(self.base_url, relative_url)

    class AmazonProductScraper(AsyncWebScraper):
        """
        Web scraper for extracting product data from Amazon.
        """
        def __init__(self):
            super().__init__("https://www.amazon.com", rate_limit=5)

        async def scrape_product(self, session, url):
            """
            Scrapes a single product page on Amazon and extracts the data.
            """
            html = await self.fetch_html(session, url) # fetch Html

            if html is None:
                return None #Could not find the URL

            soup = BeautifulSoup(html, 'html.parser')
            # Extract product data (example - adapt to the specific Amazon page structure)
            name_element = soup.find("span", class_="a-size-large product-title-word-break")
            name = name_element.text.strip() if name_element else "Not found"

            price_element = soup.find("span", class_="a-offscreen")
            price = price_element.text.strip() if price_element else "Not found"

            description_element = soup.find("div", id="productDescription_feature_div")
            description = description_element.text.strip() if description_element else "Not found"

            product_data = {
                'name': name,
                'price': price,
                'description': description,
                'source_url': url
            }

            return product_data
    ```

    Here is the worker
    ```python

            from celery import Celery
            from src import agent  # Import web scraping module
            from src import database  # Import database module
            import asyncio
            import os
            # Celery configuration
            CELERY_BROKER_URL = os.environ.get('CELERY_BROKER_URL', 'redis://localhost:6379/0') #Change that in production!
            CELERY_RESULT_BACKEND = os.environ.get('CELERY_RESULT_BACKEND', 'redis://localhost:6379/0')
            celery_app = Celery('tasks', broker=CELERY_BROKER_URL, backend=CELERY_RESULT_BACKEND)

            @celery_app.task(bind=True, retry_backoff=True, max_retries=5)
            def scrape_and_process_product(self, url):
                """
                Celery task to scrape a product page and store the data.
                """
                try:
                    # Instantiate scraper and database client
                    scraper = agent.AmazonProductScraper()
                    db_client = database.PostgreSQLClient() #You can implement context manager here for the database connection

                    # Create an event loop for scraping inside celery
                    loop = asyncio.get_event_loop()
                    product_data = loop.run_until_complete(scraper.scrape_product(url))

                    if product_data:
                        # Clean product data
                        cleaned_data = clean_product_data(product_data) #Assume

                        #Insert
                        loop.run_until_complete(db_client.insert_product(cleaned_data))
                        #Log
                        print(f'The product {url} is OK!')

                    else:
                        raise ValueError("Failed to scrape the product")

                except Exception as e:
                    # Rety this, this allows you to handle it with max retries
                    raise self.retry(exc=e, countdown=60) # Re-try in 60 seconds

                finally:
                    pass #Clean up process
    ```

        Here is the database
    ```python
        import os
        import asyncpg

        class PostgreSQLClient:
            def __init__(self):
                self.db_host = os.environ.get('DB_HOST', 'localhost')
                self.db_port = os.environ.get('DB_PORT', 5432)
                self.db_user = os.environ.get('DB_USER', 'postgres')  # For test purposes only
                self.db_pass = os.environ.get('DB_PASS', 'postgres') #For test purposes only
                self.db_name = os.environ.get('DB_NAME', 'webscraper')

            async def insert_product(self, product_data):
                """
                Inserts product data into the PostgreSQL database asynchronously.
                """
                try:
                    # Implement connection pooling (can be done using a context manager)
                    conn = await asyncpg.connect(host=self.db_host, port=self.db_port,
                                                user=self.db_user, password=self.db_pass,
                                                database=self.db_name)
                    query = """
                        INSERT INTO products (name, price, description, image_url, source_url)
                        VALUES ($1, $2, $3, $4, $5)
                    """
                    await conn.execute(query, product_data['name'], product_data['price'], product_data['description'], product_data['image_url'], product_data['source_url']) #Run query
                except Exception as e:
                    print(f"Error inserting data: {e}")

                finally:
                    if conn:
                        await conn.close()
    ```

    And last the code

    ```python
        import asyncio
        import os

        from src import worker

        if __name__ == "__main__":
            #URLs to be parsed
            product_urls = [
                "https://www.amazon.com/dp/B08L5M8X75", #Valid Link
                "https://www.amazon.com/dp/B09F82516D", #Valid Link
                "https://www.amazon.com/dp/B07G7M72MR", #Valid Link
                "https://www.amazon.com/invalid_link",   # Invalid URL (will cause an error)
            ]

            for product_url in product_urls:
                worker.scrape_and_process_product.delay(product_url) #Send the taks

            print('Successfully sended task!')
    ```

**Key Senior Developer Considerations:**

*   **Asynchronous Code:** Everything from web requests to database operations is done asynchronously to maximize concurrency and performance.
*   **Error Handling:** Robust error handling is implemented throughout the code, including handling HTTP errors, database connection errors, and data validation errors.
*   **Rate Limiting:** The `AsyncWebScraper` class implements rate limiting using an `asyncio.Semaphore` to avoid overloading the target websites.
*   **Scalability:** The use of Celery and Redis allows the system to be scaled horizontally by adding more workers to the task queue.
*    **Logging:** Has been added loggings into each step, but feel free to improve that, that is an important part to identify when and where the code fail.

