## 🧩 YAGNI (You Aren't Gonna Need It) Principle

### 📖 Definition

> **"Don't implement something until it is necessary."**

YAGNI is about avoiding building features, methods, or abstractions **just in case** you might need them later.

---

### 🚩 **Why YAGNI?**

- Prevents **overengineering**.    
- Reduces **complexity** and **wasted effort**.    
- Keeps code simple and focused on **current requirements**, not hypothetical ones.    

---

### 🛑 **Violation of YAGNI**

A common violation: creating unused methods or over-generalized code.
```
public class ReportService
{
    public void GeneratePdfReport(string data)
    {
        Console.WriteLine("Generating PDF report...");
    }

    // Added "just in case" but not needed right now!
    public void GenerateExcelReport(string data)
    {
        Console.WriteLine("Generating Excel report...");
    }

    public void GenerateCsvReport(string data)
    {
        Console.WriteLine("Generating CSV report...");
    }
}
```

**Problems:**

- Extra methods are never used.    
- Extra maintenance burden if requirements change.    
- Increases testing and documentation workload unnecessarily.    

---

### ✅ **Applying YAGNI**

Focus only on what is **required now**. Add features later when needed.

```
public class ReportService
{
    public void GeneratePdfReport(string data)
    {
        Console.WriteLine("Generating PDF report...");
    }
}
```
If later Excel support is actually needed, **add it then**:
```
public void GenerateExcelReport(string data)
{
    Console.WriteLine("Generating Excel report...");
}
```
### 🔑 Real-World Examples

- **Unnecessary interfaces:** Creating interfaces for single-use classes "just in case."    
- **Premature abstraction:** Building generic repository patterns when the app only uses one entity. 
- **Future-proofing:** Adding logging, caching, or multi-language support when there’s no requirement.    

---

### 💡 Key Takeaways

- Write **only what is necessary for current requirements**.    
- Avoid "what if we need this later?" coding.    
- YAGNI helps keep code **simple, clean, and focused**.