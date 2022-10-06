
# Coding style

## Naming conventions

Avoid using **abbreviations**.  
**Exceptions**: abbreviations commonly used as names, such as **Id**, **Xml**, **Uri**.

### Class Names
Use `PascalCasing` for class names and method names.

```C#
public class Example
{
    public void ExampleMethod()
    {
    }
}
```

### Interfaces
Use `PascalCasing` for interface name and prefix the name with an `I`.

```C#
public interface IExampleInterface
{
}
```

### Variable Names
Use `camelCasing` when naming local variables and method argumetns.

```C#
public class Example
{
    public void ExampleMethod(string exampleArgument)
    {
        var exampleLocalVariable = ...
    }
}
```
Use `camelCasing` when naming `private` or interal fields, and prefix them with `_`.

```C#
public class Example
{
    private readonly ILogger<Example> _logger;

    public Example(ILogger<Example> logger)
    {
        _logger = logger;
    }
}
```

Use `PascalCasing` when naming `public` members of types, such as fields, properties, events, methods, and local functions.


```C#
public class Example
{
    public string Name {get; set;}
}
```

### Route names
Prefer using `kebab-casing` for route names.

```C#
[HttpGet("get-all-blogs")]
```

## Lambda expressions
Use letter `x` as parameter name for lambda expressions.

If you have nested lambdas, in inner lamdas use letters `y` and `z`.

```C#
// bad 
var blogsWithPostRatingHigherThanThree = blogs
    .Where(b => b.Posts
        .Any(p => p.Rating > 3))
    .ToList();

// good
var blogsWithPostRatingHigherThanThree = blogs
    .Where(x => x.Posts
        .Any(y => y.Rating > 3))
    .ToList();
```

If you invoke a chain of methods with lambda expressions, split each invocation onto its own line. 

```C#
// bad
var evenNumbers = numbers.Where(x => x % 2 == 0).ToList();

// good
var evenNumbers = numbers
    .Where(x => x % 2 == 0)
    .ToList();
```

### LINQ conventions
Use meaningful names for query variables. The following example uses zagrebCustomers for customers who are located in Zagreb.

```C#
var zagrebCustomers = await dbContext.Customers
    .Where(x => x.City == "Zagreb")
    .ToListAsync();
```

Separate multiple `&&` from one `Where` statement in multiple `Where` statements.

```C#
// bad
var filteredNumbers = numbers
    .Where(x => x % 7 == 0 && x > 200)
    .ToList();

// good 
var filteredNumbers = numbers
    .Where(x => x % 7 == 0)
    .Where(x => x > 200)
    .ToList();
```

Use `Where` statement insted of writing condition in `Single`, or `First`.

```C#
// bad
var numberDivisibleByTen = numbers
    .FirstOrDefault(x => x % 10 == 0);

// good
var numberDivisibleByTen = numbers
    .Where(x => x % 10 == 0)
    .FirstOrDefault();
```

Prefer using `async` LINQ query when querying database.

```C#
var blogs = await dbContext.Blogs
    .Where(x => b.Rating > 3)
    .ToListAsync();
```

Use `foreach` loop over LINQ foreach.

```C#
// bad
items.ToList().ForEach(x => x.DoStuff());

// good
foreach(var item in items)
{
    item.DoStuff();
}
```

When creating new object instance in Select, add `new` keyword and class name to new line and indent it one `tab` more than `Select`.

Example: Fetch Blog name and Author name.
```C#
// bad
// loading whole object from database and using Include
var blogs = await dbContetxt.Blogs
    .Include(x => x.Author)
    .ToListAsync();

var blogData = blogs
    .Select(x => 
        new BlogData
        {
            Name = x.Name,
            AuthorName = x.Author.FirstName
        })
    .ToListAsync();

// bad
// new keyword in the same line as Select
var blogData = await dbContetxt.Blogs
    .Select(x => new BlogData 
    {
        Name = x.Name,
        AuthorName = x.Author.FirstName
    })
    .ToListAsync();

// good
var blogData = await dbContetxt.Blogs
    .Select(x => 
        new BlogData
        {
            Name = x.Name,
            AuthorName = x.Author.FirstName
        })
    .ToListAsync();
```  

From database only select data which will be used. There is no need to fetch whole object unless you are going to update it.

Avoid using `Include`. Use `Select` instead.

Do not use `Single` when fetching entities by their `primary key` from database.

## Layout conventions

Use file scoped namespaces.

Write only one statement per line.

Write only one declaration per line.

If continuation lines are not indented automatically, indent them one tab (four spaces).

Leave one blank line before return statement in method.

```C#
// bad
public int ReverseInteger(int number)
{
    var reversedNumber = 0;
    while (num > 0) 
    {
        reversedNumber = result * 10 + num % 10;
        num /= 10;
    }
    return reversedNumber;
}

// good
public int ReverseInteger(int number)
{
    var reversedNumber = 0;

    while (num > 0) 
    {
       reversedNumber = result * 10 + num % 10;
       num /= 10;
    }

    return reversedNumber;
}
```

Private methods should be last in file.

Separate type members and methods with a single blank line, except private fields.
```C#
// bad
public class Blog
{
    private string _authorName;

    private string _authorLastname;

    public Blog()
    {
    }
    public DoSomething()
    {
    }
}

// good
public class Blog
{
    private string _authorName;
    private string _authorLastname;

    public Blog()
    {
    }

    public DoSomething()
    {
    }
}
```

Use single blank lines to split method bodies into logically-related blocks.

Separate unrelated control flow blocks with a blank line.

```C#
// bad
if (value == 0)
{
}
else if (value == 1)
{
}
if (defaultValue == 0)
{
}

// good
// Separate independent `if` clauses with a blank line.
// This avoids confusion between independent conditional logic and an `else if` condition.
if (x < 0)
{
}
else if (x > 1)
{
}

if (y != 0)
{
}
```

Avoid using `else`. Always `return` before if possible.

```C#
// bad
if (someCondition) 
{
    // Some code
} 
else 
{
   // More code
}

if (someCondition)
{
    // Some code
    return;
}

// More code
```

If a single statement must include a long chain or nested tree of sub-statements, split the line as follows:

    1.Place operators at the beginning of lines rather than the end of the previous line.
    2.Use indentation to clarify nesting relationships. When splitting a parenthesized sub-expression, indent it one more level than the previous line.
    3.Split related operations at the same level.

```C#
// bad
if (blog.Rating > 3 || blog.Posts.Count() < 10 || blog.Author.Age < 30)
{
}

// good
if (blog.Rating > 3 
    || blog.Posts.Count() < 10
    || blog.Author.Age < 30)
{
}
```

Indent dotted invocations one level deeper than the root object. Place the dot for each invocation at the beginning of that line.
```C#
// bad
var blogsWithPostRatingHigherThanThree = blogs
    .Where(x => x.Posts
    .Any(y => y.Rating > 3))
    .ToList();

// bad
var blogsWithPostRatingHigherThanThree = blogs
                                    .Where(x => x.Posts
                                        .Any(y => y.Rating > 3))
                                    .ToList();
    
// good
var blogsWithPostRatingHigherThanThree = blogs
    .Where(x => x.Posts
        .Any(y => y.Rating > 3))
    .ToList();
```
Create variables when you need them.

## MediatR conventions

Use only one `SaveChanges` in handler.

Services should not call `SaveChanges`. Handler should save all changes created in services.

Throw exceptions instead of returning error codes or null in handler. 

Always validate request.


Place `Handler`, `Request` and `Response` in the same file.  
Example: `UpdateBlogNameHandler.cs` file

```C#
namespace Core.Handlers.Blog;

public class UpdateBlogNameHandler : RequestHandler<UpdateBlogNameRequest, UpdateBlogNameResponse>
{
    ...
}

public class UpdateBlogNameRequest
{
    ...
}

public class UpdateBlogNameResponse
{
    ...
}
```


## Braces

When using object/collection intializer avoid using `()`.

```C#
// bad
var person = new Person()
{
    Name = "Name"
};

// good
var person = new Person
{
    Name = "Name" 
};
```
When creating object, assing all properties in object initilaizer.

```C#
// bad
var blog = new Blog
{
    Name = "blogName"
};

blog.Rating = 5;

// good
var blog = new Blog
{
    Name = "blogName",
    Rating = 5
}
```

Use `braces` around statements with control flow keywords (`if`, `else`, `while`, `for`, `foreach`), even if statement block is one line.

```C#
// bad
if (x > 10) return;

// good
if (x > 10)
{
    return;
}
```

## Other
Always use implicit typing (`var`) for local variables.

When you have complicated expression in if statement, it is better to add this expression to the varibale and name this variable accordingly.

```C#
var list1AndList2ContainsSameElements = !list1.Except(list2).Any()
    && list2.Except(list1).Any();

if (list1AndList2ContainsSameElements)
{
    ...
}
```

Use `foreach` loop over `for` loop when is possible.

Avoid initalizing collections in loops. (`for`, `foreach`, `while`).

```C#
// bad
foreach(var number in numbers
    .Where(x => x / 2 == 0)
    .ToList())
{
    ...
}

// good
var filteredList = numbers
    .Where(x => x / 2 == 0)
    .ToList();

foreach(var number in filteredList)
{
    ...
}
```

Alawys use `IOptions` to provide strongly typed access to groups of related settings.

`appsetting,json`
```C#
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  },
  "MySettings": {
    "StringSetting": "My Value",
    "IntSetting": 23 
  }
}

public class MySettings
{
    public string StringSetting { get; set; }
    public int IntSetting { get; set; }
}
```
In service configuration just add:
```C#
public static class ServiceConfiguration
{
    public static void AddServices(this IServiceCollection services, IConfiguration configuration)
    {
        var section = configuration.GetSection("MySettings");
        services.Configure<MySettings>(section);
    }
}
```

Add changes to database context before calling `SaveChanges`.

Avoid using helper lists. Use `Select` or method with `yield return` insted.

```C#
// bad
var blogsData = new List<BlogData>();

foreach (var blog in blogs)
{
    var blogData = new BlogData
    {
        Name = blog.Name,
        Rating = blog.Rating
    };

    blogsData.Add(blogData);
}

// good
var blogsData = blogs
    .Select(x =>
        new BlogData
        {
            Name = blog.Name,
            Rating = blog.Rating
        }
    )
    .ToList();
```

Do not add meanignless comments.
```C#
/// <param name="userData">data for new user</param>
/// <returns>success result</returns>
```
