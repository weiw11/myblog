---
title: "Using Serilog with ILogger in CSharp"
tags: ["csharp", "programming", "serilog"]
date: 2025-05-06T01:56:10-04:00
draft: false
---

Serilog is a powerful logging library for .NET applications. It supports structured logging and integrates easily with `ILogger` from Microsoft.Extensions.Logging.

## Installation

Install the necessary NuGet packages:

```bash
dotnet add package Serilog.AspNetCore
dotnet add package Serilog.Sinks.Console
```

## Basic Setup in Program.cs (.NET 8+)

```csharp
using Microsoft.Extensions.Hosting;
using Serilog;

Log.Logger = new LoggerConfiguration()
    .WriteTo.Console()
    .CreateLogger();

var builder = WebApplication.CreateBuilder(args);

// Replace default logging with Serilog
builder.Host.UseSerilog();

// --- Some code here

app.Run();
```

## Using `ILogger` in a Controller or Service

```csharp
using Microsoft.Extensions.Logging;

public class MyService
{
    private readonly ILogger<MyService> _logger;

    public MyService(ILogger<MyService> logger)
    {
        _logger = logger;
    }

    public void DoWork()
    {
        _logger.LogInformation("Doing work at {Time}", DateTime.UtcNow);
        try
        {
            // some code
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred while doing work");
        }
    }
}
```

## Benefits

- Structured logging with properties
- Multiple sinks (e.g., console, file, Seq)
- Easy integration with ASP.NET Core

## Resources

- [Serilog Documentation](https://serilog.net/)
- [GitHub Repo](https://github.com/serilog/serilog-aspnetcore)
