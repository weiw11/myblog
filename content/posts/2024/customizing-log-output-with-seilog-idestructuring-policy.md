---
title: "Customizing Log Output with Seilog's IDestructuring Policy"
tags: ["csharp", "programming", "serilog"]
date: 2024-08-06T13:02:35-04:00
draft: false
---

Serilog is a powerful logging library for .NET that allows deep customization of how objects are represented in structured logs. One such customization point is `IDestructuringPolicy`.

## What is `IDestructuringPolicy`?

`IDestructuringPolicy` is an interface in Serilog that lets you control how specific objects are transformed into structured log data. This is especially useful when you want to:

- Omit sensitive fields (e.g., passwords, tokens)
- Rename properties
- Format output differently

## Use Case Example

Say you have a `User` class:

```csharp
// User.cs
public class User
{
    public string Username { get; set; }
    public string Password { get; set; }
}
```

By default, Serilog logs both fields if you use destructuring syntax `{@user}`. For sensitive information like passwords, it's best practice to remove or omit the value from being logged.

## Creating a Custom IDestructuringPolicy

```csharp
// RedactSensitiveDataPolicy.cs
public class RedactSensitiveDataPolicy : IDestructuringPolicy
{
    public bool TryDestructure(object value, ILogEventPropertyValueFactory propertyValueFactory, out LogEventPropertyValue result)
    {
        if (value is User user)
        {
            var props = new[]
            {
                new LogEventProperty("Username", new ScalarValue(user.Username)),
                new LogEventProperty("Password", new ScalarValue("***REDACTED***"))
            };

            result = new StructureValue(props);
            return true;
        }

        result = null;
        return false;
    }
}
```

## Registering the Policy

```csharp
// Program.cs
Log.Logger = new LoggerConfiguration()
    .Destructure.With<RedactSensitiveDataPolicy>()
    .WriteTo.Console()
    .CreateLogger();
```

## Sample Output

```csharp
[01:45:17 INF] Retrieved user {"Username": "johndoe", "Password": "***REDACTED***"}
```

## Tips

- You can apply multiple policies.
- Order matters—Serilog uses the first applicable policy.
- For general-purpose redaction, consider checking field names and types dynamically.

## Resources

- [Customizing the stored data](https://github.com/serilog/serilog/wiki/Structured-Data#customizing-the-stored-data)