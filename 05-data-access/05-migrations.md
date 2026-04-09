# 05: Migrations

## What problem does this solve?

Developers often face challenges when managing database schema changes across different environments or versions of an application. Manual SQL scripts can be error-prone, time-consuming, and difficult to maintain as applications evolve. Migrations provide a structured way to automate these changes, ensuring consistency between development, testing, and production databases.

## The Big Idea

Imagine your application's data model as a house with rooms (tables) and furniture (columns). As the house grows or changes, you need to add new rooms, rearrange existing ones, or update their layout. Migrations are like blueprints that guide these transformations systematically, ensuring everyone involved knows exactly what needs to be done.

## Mechanics Relevant to Developers

Migrations in .NET (e.g., Entity Framework Core) are essentially versioned scripts that represent changes to the database schema. Each migration is a C# class with two key methods: `Up()` and `Down()`. The `Up()` method applies the change, while the `Down()` method reverses it, allowing for easy rollback if needed.

### Key Concepts

- **Migration Files**: These are automatically generated files that contain instructions to modify the database schema.
- **Context Class**: Acts as a bridge between your application's data model and the database. It tracks which migrations have been applied.
- **Database Provider**: Migrations work with various providers (e.g., SQL Server, PostgreSQL) by translating C# code into provider-specific SQL.

### How Migrations Work

1. **Add-Migration**: Creates a new migration file based on changes in your data model.
2. **Update-Database**: Applies pending migrations to the database.
3. **Remove-Migration**: Reverts the last applied migration (if needed).

## Code Example

```csharp
// Example: Adding a new column to an existing table using EF Core Migrations.

using Microsoft.EntityFrameworkCore.Migrations;

namespace MyApp.Data.Migrations
{
    public partial class AddEmailColumn : Migration
    {
        protected override void Up(MigrationBuilder migrationBuilder)
        {
            // Adds a new Email column to the Users table.
            migrationBuilder.AddColumn<string>(
                name: "Email",
                table: "Users",
                type: "nvarchar(max)",
                nullable: true);
        }

        protected override void Down(MigrationBuilder migrationBuilder)
        {
            // Reverts the change by removing the Email column.
            migrationBuilder.DropColumn(
                name: "Email",
                table: "Users");
        }
    }
}
```

### Explanation

- **Up() Method**: Adds a new `Email` column to the `Users` table. The `type` parameter specifies the SQL type, and `nullable: true` allows null values.
- **Down() Method**: Reverses the change by dropping the newly added column. This ensures reversibility in case of errors or rollbacks.

## Common "Gotchas"

1. **Forgetting to Apply Migrations**: Running `Update-Database` after generating migrations is crucial; otherwise, changes won't be applied.
2. **Schema Drift**: If manual SQL scripts are used alongside migrations, the database schema may diverge from what migrations expect, leading to conflicts.
3. **Data Loss During Rollback**: When rolling back a migration that involves data loss (e.g., dropping a column), ensure backups or alternative solutions are in place.

## Opinionated Advice

- **Version Control Migrations**: Treat migration files as code and commit them to version control. This ensures consistency across team members.
- **Avoid Complex Logic in Migrations**: Keep migrations simple and focused on schema changes. Avoid embedding business logic or data manipulation here.
- **Test Migrations Locally First**: Always apply migrations locally before deploying to ensure they work as expected.

## When Defaults Are Enough

- **Simple Schema Changes**: For straightforward additions like new columns, tables, or indexes, the default migration behavior is usually sufficient without customization.
- **Development Environments**: In local development, where databases are often recreated frequently, manual intervention may not be necessary.

## Boundaries of Overkill

- **Overcustomizing Migrations**: Adding excessive logic (e.g., data transformations) to migrations can make them brittle and hard to maintain. Use separate scripts or tools for complex data operations.
- **Ignoring Database Constraints**: Relying solely on migrations without considering database-level constraints (e.g., foreign keys, unique indexes) can lead to inconsistencies.

## Summary

Migrations are a powerful tool for managing database schema changes in .NET applications. They provide a structured approach to versioning and applying these changes across environments. However, developers must be mindful of their limitations and use them judiciously to avoid common pitfalls.

### Take-home Value

1. Migrations automate database schema changes, reducing manual effort and errors.
2. Each migration is reversible via the `Up()` and `Down()` methods for safe rollbacks.
3. Always apply migrations using `Update-Database` after generating them.
4. Keep migrations simple and focused on schema changes; avoid embedding complex logic.
5. Test migrations locally before deploying to production environments.
6. Be cautious of data loss during rollbacks, especially when dropping columns or tables.
