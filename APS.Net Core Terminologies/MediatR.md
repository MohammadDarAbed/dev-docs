## 🧭 MediatR — Overview

**MediatR** is a simple, powerful .NET library that implements the **Mediator Design Pattern**.  
It helps you **decouple the communication between different parts** of your application (like Controllers, Services, Handlers).

---

## 💡 Why Use MediatR?

- Decouples components: No direct communication between layers    
- Clean architecture & separation of concerns    
- Makes code easier to test and maintain    
- Encourages **CQRS (Command and Query Responsibility Segregation)**    

---

## 🧠 Core Concepts

|Concept|Description|
|---|---|
|`Request`|The message/command/query sent to MediatR|
|`Handler`|The logic that handles the request|
|`IMediator`|The interface used to send messages via MediatR|
|`Notification`|A broadcast message that multiple handlers can respond to|

---

## 📦 MediatR Request Types

```
IRequest<TResponse>   → Used for queries or commands that return a result
IRequest              → Used for commands that do not return a result
INotification         → Used for publish/subscribe (notifications)
```

---

## 🎯 Real-World Analogy

> Imagine a **Help Desk** in a company:

- You (the controller) want to request something, like fetching employee details.    
- Instead of contacting HR directly, you send your request to the **Help Desk (Mediator)**.    
- The Help Desk forwards your request to the right department (Handler).    
- You receive the response **without knowing who handled it or how**.    

---

## 📘 Example: Get User by ID (Query)

### 1. **Request (Query)**

> "Get user details by ID"

```
public class GetUserByIdQuery : IRequest<User>
{
    public int Id { get; set; }
}
```

### 2. **Handler**

> This is the person in HR who knows how to find the user

```
public class GetUserByIdHandler : IRequestHandler<GetUserByIdQuery, User>
{
    public Task<User> Handle(GetUserByIdQuery request, CancellationToken cancellationToken)
    {
        // Fetch user from database
    }
}
```

### 3. **Controller**

> You don’t contact the handler directly, you talk to the Help Desk

```
public class UsersController : ControllerBase
{
    private readonly IMediator _mediator;

    public UsersController(IMediator mediator)
    {
        _mediator = mediator;
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> Get(int id)
    {
        var user = await _mediator.Send(new GetUserByIdQuery { Id = id });
        return Ok(user);
    }
}
```
---

## 📌 Benefits Recap

- ✅ Clean architecture (no tightly coupled code)    
- ✅ Easy to extend and refactor    
- ✅ Highly testable (you can test handlers in isolation)    
- ✅ Scales well in large applications    

---

## 🧱 When to Use MediatR

- When you follow **CQRS** principles    
- When you want **decoupled logic**    
- In **modular systems** or microservices    
- When business logic needs to be reusable and testable    

---

## 🔄 Optional Extensions

- MediatR supports **pipelines** (e.g. logging, validation)    
- You can use behaviors to add middleware-like logic