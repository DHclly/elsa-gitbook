---
description: >-
  In this topic, we'll setup a simple Console and an ASP.NET Core application
  that can host and execute workflows.
---

# Hello World

## Console <a href="#setup" id="setup"></a>

{% stepper %}
{% step %}
#### Create Console App

Start by creating a new console application:

```bash
dotnet new console -n "ElsaConsole"
```
{% endstep %}

{% step %}
#### Add Packages

Navigate to your newly created project's root directory and add the following packages:

```bash
cd ElsaConsole
dotnet add package Elsa
```
{% endstep %}

{% step %}
**Modify Program.cs**

Open `Program.cs` and replace its contents with the following:

```csharp
using Elsa.Extensions;
using Microsoft.Extensions.DependencyInjection;

// Setup service container.
var services = new ServiceCollection();

// Add Elsa services to the container.
services.AddElsa();

// Build the service container.
var serviceProvider = services.BuildServiceProvider();

// Instantiate an activity to run.
var activity = new WriteLine("Hello World!");

// Resolve a workflow runner to execute the activity.
var workflowRunner = serviceProvider.GetRequiredService<IWorkflowRunner>();

// Execute the activity.
await workflowRunner.RunAsync(activity);
```

This code sets up a service container and adds Elsa services to it. The `serviceProvider` can be used to resolve Elsa services and run workflows.
{% endstep %}
{% endstepper %}

## ASP.NET Core

{% stepper %}
{% step %}
**Create the Project**

Create a new empty ASP.NET app using the following command:

```bash
dotnet new web -n "ElsaWeb"
```
{% endstep %}

{% step %}
**Add Packages**

Navigate to your project's root directory and install the Elsa package:

```bash
cd ElsaWeb
dotnet add package Elsa
dotnet add package Elsa.Http
```
{% endstep %}

{% step %}
**Modify Program.cs**

Open `Program.cs` in your project and replace its contents with the code provided below.

**Program.cs**

```csharp
using Elsa.Extensions;
using ElsaWeb.Workflows;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddElsa(elsa =>
{
    elsa.AddWorkflow<HttpHelloWorld>();
    elsa.UseHttp();
});

var app = builder.Build();

// Configure the HTTP request pipeline.
app.UseWorkflows();
app.Run();
```
{% endstep %}

{% step %}
**Add HttpHelloWorld Workflow**

Create a new directory called `Workflows` and add a new file to it called `HttpHelloWorld.cs` with the following.

**Workflows/HttpHelloWorld.cs**

```csharp
using Elsa.Http;
using Elsa.Workflows;
using Elsa.Workflows.Activities;
using Elsa.Workflows.Contracts;

namespace ElsaWeb.Workflows;

public class HttpHelloWorld : WorkflowBase
{
    protected override void Build(IWorkflowBuilder builder)
    {
        builder.Root = new Sequence
        {
            Activities =
            {
                new HttpEndpoint
                {
                    Path = new("/hello-world"),
                    CanStartWorkflow = true
                },
                new WriteHttpResponse
                {
                    Content = new("Hello world of HTTP workflows!")
                }
            }
        };
    }
}
```
{% endstep %}
{% endstepper %}

## Summary <a href="#summary" id="summary"></a>

This document explains setting up Console and ASP.NET Core apps using Elsa workflows. For the Console app, we configured a service container, added Elsa, and ran a "Hello World" workflow. The ASP.NET Core app integrates Elsa with HTTP endpoints to process workflows. Follow the code samples for package additions and `Program.cs` configurations. Refer to source code links for further details.

## Source Code

* [Console app](https://github.com/elsa-workflows/elsa-guides/tree/main/src/installation/elsa-console)
* [ASP.NET Core app](https://github.com/elsa-workflows/elsa-guides/tree/main/src/installation/elsa-web)
