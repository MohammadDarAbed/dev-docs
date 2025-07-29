# 🔐 **What is Policy-Based Authorization?**

Policy-based authorization is a way to control who can access certain parts of your application by using custom rules (called **policies**). Instead of just checking roles like "Admin", you can define more flexible rules, like:

- The user must have a certain **claim** (e.g., country = "Palestine")    
- The user must meet a condition (e.g., age ≥ 18)    
- The user must pass custom logic (like checking a database value)

### 🧩 Simple Explanation

Think of a **policy** as a named rule. You give it a name (like `"OnlyAdults"`), define what the rule is, and then apply it to your controllers or endpoints.

🔁 Example:

- **Policy Name**: `"OnlyAdults"`    
- **Rule**: User must be 18 years old or older    
- **Use it like**: `[Authorize(Policy = "OnlyAdults")]`
### 🔑 Key Concepts

1. **Policy**: A named set of rules that a user must meet to access a resource.    
2. **Requirement**: A condition that must be fulfilled (e.g., having a specific claim or meeting a custom rule).    
3. **Handler**: The logic that checks whether a requirement is met.    
4. **Authorization Service**: Used to check if the user meets the policy.
    

---

# ✅ Example Use Case

Suppose you want only users who are at least 18 years old to access a certain endpoint.
### 🛠️ Step-by-Step Overview

#### 1. **Define a Requirement**


```
public class MinimumAgeRequirement : IAuthorizationRequirement
{
    public int MinimumAge { get; }
    public MinimumAgeRequirement(int minimumAge) => MinimumAge = minimumAge;
}
```

---

#### 2. **Create a Handler**

```
public class MinimumAgeHandler : AuthorizationHandler<MinimumAgeRequirement>
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        MinimumAgeRequirement requirement)
    {
        var birthDateClaim = context.User.FindFirst(c => c.Type == "DateOfBirth");
        if (birthDateClaim == null)
            return Task.CompletedTask;

        var birthDate = DateTime.Parse(birthDateClaim.Value);
        int age = DateTime.Today.Year - birthDate.Year;
        if (birthDate > DateTime.Today.AddYears(-age)) age--;

        if (age >= requirement.MinimumAge)
            context.Succeed(requirement);

        return Task.CompletedTask;
    }
}
```

---

#### 3. **Register the Policy in `Program.cs`**

```
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("AtLeast18", policy =>
        policy.Requirements.Add(new MinimumAgeRequirement(18)));
});

builder.Services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();
```
---

#### 4. **Use the Policy in a Controller**

```
[Authorize(Policy = "AtLeast18")]
[HttpGet("restricted")]
public IActionResult GetRestrictedContent()
{
    return Ok("You are old enough to see this content.");
}
```
---

### 🧠 Benefits

- Reusable and modular    
- Supports complex business logic    
- More maintainable than hardcoding roles or claims everywhere