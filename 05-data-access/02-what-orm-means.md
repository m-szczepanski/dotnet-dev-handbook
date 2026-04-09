# 02: What ORM Actually Means

## Introduction

ORM stands for Object-Relational Mapping, which is a technique used to bridge the gap between object-oriented programming (OOP) and relational databases. In simple terms, it allows developers to work with database data using objects instead of writing raw SQL queries.

## The Big Idea: A Real-World Analogy

Imagine you're managing a library where books are stored in shelves organized by categories like "Fiction," "Non-Fiction," etc. Now, suppose you want to lend out books or add new ones. Instead of manually navigating through the physical shelves and writing down changes on paper (like SQL queries), ORM is like having a librarian who understands both your spoken requests ("I need all mystery novels") and how to efficiently retrieve those books from the shelves.

In this analogy:

- **Your Request** = Object-oriented code in C#
- **The Librarian** = The ORM tool
- **The Shelves (Database)** = Relational database tables

ORM acts as a translator, converting your object-based requests into SQL queries and then mapping the results back into objects you can work with.

## Mechanics Relevant to Developers

## How ORM Works

1. Mapping Objects to Tables:
   - Each class in your code (e.g., `Book`, `Author`) is mapped to a corresponding table in the database.
   - Properties of the class correspond to columns in the table.
2. **Querying Data**:
   - Instead of writing SQL queries, you use methods or LINQ expressions on objects.
   - The ORM tool translates these into efficient SQL queries under the hood.
3. **Saving Changes**:
   - When you modify an object (e.g., update a book's title), the ORM automatically generates and executes SQL statements to persist those changes in the database.
4. **Handling Relationships**:
   - ORM can manage relationships between tables, such as one-to-many or many-to-many.
   - For example, if `Author` has multiple `Books`, the ORM handles fetching all books for a given author seamlessly.

## Key Components

- **Entity Classes**: Represent database tables (e.g., `Book`, `Author`).
- **Context Class**: Acts as the entry point to interact with the database.
- **Query Language**: Often uses LINQ or similar constructs to query data.

## **Code Example**

Here's a simple example using Entity Framework Core, one of the most popular ORMs in .NET:

```csharp
// Define an entity class (Book)
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; } = null!;
    public DateTime PublishedDate { get; set; }

    // Navigation property to Author
    public int AuthorId { get; set; }
    public Author? Author { get; set; }
}

// Define another entity class (Author)
public class Author
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;
    
    // Navigation property to Books
    public List<Book>? Books { get; set; }
}

// Context class representing the database
public class LibraryContext : DbContext
{
    public DbSet<Book> Books { get; set; } = null!;
    public DbSet<Author> Authors { get; set; } = null!;

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        // Use SQLite for simplicity in this example
        optionsBuilder.UseSqlite("Data Source=Library.db");
    }
}

// Example usage: Fetching books by author
public class Program
{
    public static async Task Main(string[] args)
    {
        using var context = new LibraryContext();

        // Query all books written by a specific author
        string authorName = "J.K. Rowling";
        var booksByAuthor = await context.Books
            .Include(b => b.Author)  // Include related Author data
            .Where(b => b.Author?.Name == authorName)
            .ToListAsync();

        foreach (var book in booksByAuthor)
        {
            Console.WriteLine($"{book.Title} by {book.Author?.Name}");
        }
    }
}
```

## Explanation

- **Entity Classes (`Book`, `Author`)**: Represent tables in the database.
- **Context Class (`LibraryContext`)**: Manages interactions with the database.
- **LINQ Query**: Instead of writing raw SQL, we use LINQ to query books by author. The ORM translates this into an efficient SQL query under the hood.

## Common "Gotchas"

1. **N+1 Queries**:
   - When fetching related data (e.g., all books for each author), ensure you use `Include` or lazy loading properly to avoid multiple database roundtrips.
2. **Over-Querying**:
   - Be mindful of what data you're retrieving. Fetching unnecessary fields can lead to performance issues.
3. **Complex Mapping**:
   - ORM works best with simple, normalized databases. Complex schema designs might require custom mapping or manual SQL queries.
4. **Performance Overhead**:
   - While ORMs simplify development, they can introduce overhead compared to raw SQL in high-performance scenarios.

## Opinionated Advice

- **Use ORM for CRUD Operations**: For basic Create, Read, Update, and Delete operations, ORMs are ideal.
- **Optimize Queries with LINQ**: Leverage LINQ's capabilities but be mindful of performance implications (e.g., use `Include` wisely).
- **Avoid Over-Mapping Complex Data**: If your database schema is highly complex or denormalized, consider using raw SQL for better control.
- **Profile ORM Performance**: Use tools like Entity Framework Core's logging to understand the generated SQL queries and optimize as needed.

## When Defaults Are Enough

ORMs are particularly useful in scenarios where:

- You're working with a standard relational database schema.
- Your application primarily performs CRUD operations.
- You want to focus on business logic rather than writing raw SQL.

For example, in most web applications that manage user data or product catalogs, the default ORM capabilities (like Entity Framework Core) are sufficient for handling basic data access needs.

## Boundaries of Overkill

ORMs can become overkill when:

- You're working with highly complex database schemas where manual control is necessary.
- Performance is critical, and every query must be optimized to the millisecond.
- The application requires advanced SQL features (e.g., stored procedures or custom indexing) that aren't easily mapped through ORM.

In such cases, consider using raw SQL queries or a hybrid approach where you use ORM for simple operations but fall back to manual SQL for complex scenarios.

## Summary

ORM is a powerful tool that simplifies data access by allowing developers to work with objects instead of writing raw SQL. It bridges the gap between object-oriented programming and relational databases, making it easier to manage entities, relationships, and queries. However, like any tool, it has its limitations, especially in complex or performance-critical scenarios.

### Take-home Value

1. ORM stands for Object-Relational Mapping.
2. It translates object-based operations into SQL queries automatically.
3. Use ORMs for CRUD operations but be mindful of performance implications.
4. Avoid over-mapping when dealing with complex database schemas.
5. Profile and optimize ORM-generated queries to ensure efficiency.
6. Consider raw SQL or a hybrid approach in high-performance scenarios.
