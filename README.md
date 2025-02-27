"# Go_lang" 
Topics
-Hello golang
-web-service-gin, Developing a RESTful API with Go and Gin
Gin simplifies many coding tasks associated with building web applications, including web services. In this tutorial, you’ll use Gin to route requests, retrieve request details, and marshal JSON for responses.

    Design API endpoints.
    Create a folder for your code.
    Create the data.
    Write a handler to return all items.
    Write a handler to add a new item.
    Write a handler to return a specific item.

Using Go (Golang) with SQL databases is a common task for building backend services and applications. Go provides a standard library package called database/sql that offers a generic interface for working with SQL databases. Additionally, you can use database-specific drivers to connect to your preferred SQL database (e.g., MySQL, PostgreSQL, SQLite).

1. Install a Database Driver
Go's database/sql package requires a database-specific driver to interact with your SQL database. Some popular drivers include:
    MySQL: github.com/go-sql-driver/mysql
    PostgreSQL: github.com/lib/pq
    SQLite: github.com/mattn/go-sqlite3

Install the driver using go get:
go get -u github.com/go-sql-driver/mysql  # For MySQL
go get -u github.com/lib/pq               # For PostgreSQL
go get -u github.com/mattn/go-sqlite3     # For SQLite

2. Import Required Packages
In your Go code, import the database/sql package and the appropriate driver.

import (
    "database/sql"
    _ "github.com/go-sql-driver/mysql" // MySQL driver (underscore imports it for side effects)
    "fmt"
    "log"
)

3. Connect to the Database
Use the sql.Open function to establish a connection to the database. The connection string format depends on the database you're using.

Example for MySQL:
db, err := sql.Open("mysql", "username:password@tcp(127.0.0.1:3306)/dbname")
if err != nil {
    log.Fatal(err)
}
defer db.Close() // Ensure the connection is closed when done

Example for PostgreSQL:
db, err := sql.Open("postgres", "user=username dbname=dbname sslmode=disable")
if err != nil {
    log.Fatal(err)
}
defer db.Close()

4. Execute SQL Queries
You can use the db.Query, db.Exec, and db.QueryRow methods to execute SQL queries.
Example: Querying Data
rows, err := db.Query("SELECT id, name FROM users")
if err != nil {
    log.Fatal(err)
}
defer rows.Close()

for rows.Next() {
    var id int
    var name string
    if err := rows.Scan(&id, &name); err != nil {
        log.Fatal(err)
    }
    fmt.Printf("ID: %d, Name: %s\n", id, name)
}

if err := rows.Err(); err != nil {
    log.Fatal(err)
}

Example: Inserting Data
result, err := db.Exec("INSERT INTO users (name, email) VALUES (?, ?)", "John Doe", "john@example.com")
if err != nil {
    log.Fatal(err)
}

lastInsertID, err := result.LastInsertId()
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Inserted row with ID: %d\n", lastInsertID)

Example: Updating Data
result, err := db.Exec("UPDATE users SET email = ? WHERE id = ?", "john.doe@example.com", 1)
if err != nil {
    log.Fatal(err)
}

rowsAffected, err := result.RowsAffected()
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Updated %d rows\n", rowsAffected)

Example: Deleting Data
result, err := db.Exec("DELETE FROM users WHERE id = ?", 1)
if err != nil {
    log.Fatal(err)
}

rowsAffected, err := result.RowsAffected()
if err != nil {
    log.Fatal(err)
}
fmt.Printf("Deleted %d rows\n", rowsAffected)

5. Handle Errors and Transactions
Always handle errors properly and use transactions when performing multiple operations that need to be atomic.
Example: Using Transactions
tx, err := db.Begin()
if err != nil {
    log.Fatal(err)
}

// Perform multiple operations
_, err = tx.Exec("INSERT INTO users (name, email) VALUES (?, ?)", "Jane Doe", "jane@example.com")
if err != nil {
    tx.Rollback() // Rollback on error
    log.Fatal(err)
}

_, err = tx.Exec("UPDATE users SET email = ? WHERE id = ?", "jane.doe@example.com", 2)
if err != nil {
    tx.Rollback() // Rollback on error
    log.Fatal(err)
}

err = tx.Commit() // Commit the transaction
if err != nil {
    log.Fatal(err)
}

6. Best Practices
    Use prepared statements (db.Prepare) for repeated queries to improve performance and security.
    Always close database connections, rows, and transactions to avoid resource leaks.
    Validate and sanitize user inputs to prevent SQL injection attacks.
    Use connection pooling (enabled by default in database/sql) for better performance in production.

Example Full Program
Here’s a complete example for querying data from a MySQL database:

package main

import (
    "database/sql"
    _ "github.com/go-sql-driver/mysql"
    "fmt"
    "log"
)

func main() {
    // Connect to the database
    db, err := sql.Open("mysql", "username:password@tcp(127.0.0.1:3306)/dbname")
    if err != nil {
        log.Fatal(err)
    }
    defer db.Close()

    // Query the database
    rows, err := db.Query("SELECT id, name FROM users")
    if err != nil {
        log.Fatal(err)
    }
    defer rows.Close()

    // Iterate over the results
    for rows.Next() {
        var id int
        var name string
        if err := rows.Scan(&id, &name); err != nil {
            log.Fatal(err)
        }
        fmt.Printf("ID: %d, Name: %s\n", id, name)
    }

    // Check for errors after iteration
    if err := rows.Err(); err != nil {
        log.Fatal(err)
    }
}








