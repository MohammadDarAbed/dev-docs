>***==It's the smallest unit: The role is a group of permissions.
> It's not have a build in implementation.==***

### 📖 What is Permission-Based Authorization?

**Permission-Based Authorization** is an authorization strategy that grants or denies access based on **fine-grained permissions** (or claims) rather than just roles.

- Unlike **Role-Based Authorization** (which grants access based on roles like `Admin` or `User`), permission-based authorization allows **specific actions** (e.g., `CanCreateProduct`, `CanDeleteUser`) to be controlled individually.
    
- This is more flexible and is ideal for **complex systems** where roles alone are not sufficient.

### 🔐 Key Concepts

1. **Permissions vs. Roles**:
    
    - **Roles**: Coarse-grained (e.g., "Admin", "User").        
    - **Permissions**: Fine-grained (e.g., "CanEditProduct", "CanViewOrders").
        
2. **Claims-Based Identity**:
    
    - Permissions are usually implemented using **claims** in the user's identity. For example, a JWT token may include:
        
```
        "permissions": ["Product.Edit", "Order.View"]
```
        
3. **Custom Authorization Handler**:
    
    - You create custom policies using `IAuthorizationHandler` or `AuthorizationHandler<TRequirement>` to check for specific permissions.
        
4. **Authorization Policy**:
    
    - Register custom policies in `Startup.cs` or `Program.cs`:

```
services.AddAuthorization(options =>
{
    options.AddPolicy("CanEditProduct", policy =>
        policy.Requirements.Add(new PermissionRequirement("Product.Edit")));
});

```

---

### 🏗 How It Works

1. Each **user** is assigned **permissions** (directly or through roles).    
2. Each **API endpoint or resource** checks for specific permissions.    
3. If the user has the required permission → **Access granted**; otherwise → **Access denied**.
 
---

### 🔑 Example Scenario

- **Role-Based:**    
    - User has `Admin` role → Access everything.        
- **Permission-Based:**    
    - User has these permissions:        
        - ✅ `CanViewProducts`            
        - ❌ `CanDeleteProducts`            
    - Even if they have a role like `Manager`, they can only do what their **permissions explicitly allow**.

---

### ✅ Implementation Steps

1. **Define Permission Requirement**:
    
```
public class PermissionRequirement : IAuthorizationRequirement
{
    public string Permission { get; }
    public PermissionRequirement(string permission) => Permission = permission;
}

```
    
2. **Create Authorization Handler**:
    
```
public class PermissionRequirement : IAuthorizationRequirement
{
    public string Permission { get; }
    public PermissionRequirement(string permission) => Permission = permission;
}

```
    
3. **Register the Handler**:
    
```
    services.AddSingleton<IAuthorizationHandler, PermissionHandler>();
```
    
4. **Apply Policy to Controllers or Actions**:
    
```
[Authorize(Policy = "CanEditProduct")]
public IActionResult EditProduct(int id) => View();

```


---
### Example:
## 🧩 Implementation in ASP.NET Core

### 1️⃣ Define Permissions

You can define permissions as constants or an enum:

```
public static class Permissions
{
    public const string ViewProducts = "Permissions.Products.View";
    public const string CreateProducts = "Permissions.Products.Create";
    public const string DeleteProducts = "Permissions.Products.Delete";
}
```

---

### 2️⃣ Add Permissions as Claims

When creating the JWT token (or user identity), include permission claims:

```
var claims = new List<Claim>
{
    new Claim("Permission", Permissions.ViewProducts),
    new Claim("Permission", Permissions.CreateProducts)
};
```

---

### 3️⃣ Create a Custom Authorization Policy

```
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("CanViewProducts", policy =>
        policy.RequireClaim("Permission", Permissions.ViewProducts));

    options.AddPolicy("CanCreateProducts", policy =>
        policy.RequireClaim("Permission", Permissions.CreateProducts));
});
```

---

### 4️⃣ Apply Policy on Controllers/Endpoints

```
[Authorize(Policy = "CanViewProducts")]
[HttpGet("products")]
public IActionResult GetProducts() => Ok("Products list");

[Authorize(Policy = "CanCreateProducts")]
[HttpPost("products")]
public IActionResult CreateProduct() => Ok("Product created");
```

---

### 5️⃣ Advanced: Custom Permission Handler

For more flexibility:

```
public class PermissionRequirement : IAuthorizationRequirement
{
    public string Permission { get; }
    public PermissionRequirement(string permission) => Permission = permission;
}

public class PermissionHandler : AuthorizationHandler<PermissionRequirement>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context, PermissionRequirement requirement)
    {
        if (context.User.HasClaim(c => c.Type == "Permission" && c.Value == requirement.Permission))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }
}
```

**Register handler:**


```
builder.Services.AddSingleton<IAuthorizationHandler, PermissionHandler>();
```

---
### 🧩 Advantages of Permission-Based Authorization

- More scalable and maintainable than role-based systems for complex applications.    
- Supports dynamic permissions fetched from a database or an external provider.    
- Easily integrates with identity providers via claims.