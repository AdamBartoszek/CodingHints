---
title: "To throw or not to throw?"
date: 2023-02-03
---

Have you ever wondered how to handle incorrect behaviors in our code? 
We can distinguish two most common ways: **Exceptions** and **OperationResult** pattern.
Which is better? Let's check!

### Performance
Have you heard that throwing&catching exceptions in .Net are heavy? Have you ever checked that? I have.

I considered two aspects: 

**Depth** - What is a depth between 'catch' and 'throw'.

Example:
```javascript
try { throw new Exception(); } catch {} // depth: 0
try { someExceptionThrowingFunction(); } catch {} // depth: 1
try { someFunctionWhichExecutesTheOtherExceptionThrowingFunction(); } catch {} // depth: 2
// ect...
``` 
**Iterations** - how many times I executed the same code.

**The results:**
| Scenario | Throw | OperationResult |
|-------|--------|---------|
| Depth level: 1, iterations: 10000 | 81ms | 1ms |
| Depth level: 10, iterations: 10000 | 151ms | 1ms |
| Depth level: 100, iterations: 10000 | 947ms | 10ms |
| Depth level: 1, iterations: 100000 | 701ms | 1ms |
| Depth level: 10, iterations: 100000 | 1513ms | 9ms |
| Depth level: 100, iterations: 100000 | 8049ms | 92ms |

### XYZ
