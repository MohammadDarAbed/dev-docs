## Authorize:
[Authorize] :   The `[Authorize]` attribute in ASP.NET Core is used to **restrict access** to controllers or actions based on the **user's authentication and authorization status**.

> We can put it on the endpoint or on the whole controller. 
### 🔐 Summary of `[Authorize]` Attribute:

- Ensures that **only authenticated users** can access certain endpoints.    
- Can be customized to allow access based on **roles**, **policies**, or **claims**.    
- Used on **controllers**, **actions**, or globally.

✅ Basic Example

```
[Authorize]
public class DashboardController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```
🟡 This ensures that **only logged-in users (Authorized)** can access the `DashboardController`.
### 🔐 Role-based Authorization

```
[Authorize(Roles = "Admin")]
public IActionResult AdminPanel()
{
    return View();
}
```
🟢 Only users with the **"Admin" role** can access this action.

---

### 📋 Policy-based Authorization

Assume you’ve defined a policy called `"CanEdit"` in `Startup.cs` or `Program.cs`:

```
services.AddAuthorization(options =>
{
    options.AddPolicy("CanEdit", policy => policy.RequireClaim("Permission", "Edit"));
});

```
Then use:

```
[Authorize(Policy = "CanEdit")]
public IActionResult EditPage()
{
    return View();
}

```

---


## AllowAnonymous:
### ❌ Allow Anonymous Access

To **override** `[Authorize]` and allow access for any user without authorization:

```
[AllowAnonymous]
public IActionResult Login()
{
    return View();
}
```
for example we use it for the login endpoint.
