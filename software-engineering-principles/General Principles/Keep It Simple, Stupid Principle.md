### 📖 Definition

> **Systems work best if they are kept simple rather than made complicated. Simplicity should be a key goal in design.**

KISS encourages writing **clear, straightforward code** that is easy to understand, maintain, and extend.

---

### 🚩 **Why KISS?**

- Simple code is easier to read, test, and debug.    
- Reduces risk of bugs caused by complexity.    
- Makes maintenance and onboarding easier.    

---

### 🛑 **Violation of KISS**

A common violation is writing overly complex logic where a simple solution exists.
```
public bool IsEven(int number)
{
    if (number % 2 == 0)
        return true;
    else
        return false;
}
```
**Problem:**

- The above is verbose and unnecessarily complex for a simple check.    

---

### ✅ **Applying KISS**

Simplify the code by returning the expression directly.
```
public bool IsEven(int number)
{
    return number % 2 == 0;
}
```

---

### 🔑 Real-World Examples

- Using built-in language features instead of reinventing the wheel.    
- Avoiding deep nested loops or conditional statements by breaking code into smaller methods.  
- Preferring clear and concise naming over clever but obscure ones.    

---

### 💡 Key Takeaways

- Favor **clarity over cleverness**.    
- Write code for **humans to read** first, machines second.    
- Keep design and implementation **as simple as possible, but no simpler**.