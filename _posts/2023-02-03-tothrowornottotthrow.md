---
title: "To throw or not to throw?"
date: 2023-02-03
---

Have you ever wondered how to handle incorrect behaviors in our code? 
We can distinguish two most common ways: **Exceptions** and **OperationResult** pattern.
Which is better? Let's check!

### Performance
Have you heard that throwing&catching exceptions in .Net are heavy? Have you ever checked that? I have.

I considered two XYZ: 
**Depth** - What is a depth between 'catch' and 'throw'.
Example:
```javascript
try { throw new Exception(); } catch {} // depth: 0
try { someExceptionThrowingFunction(); } catch {} // depth: 1
try { someFunctionWhichExecutesTheOtherExceptionThrowingFunction(); } catch {} // depth: 2
// ect...
``` 
**Iterations** - how many times I executed the same code.
I compared how long it takes to XExecutions o

**The results:**
Depth level: 1, iterations: 10000
Throw: 81ms
OperatioResult: 0ms

Depth level: 10, iterations: 10000
Throw: 151ms
OperatioResult: 0ms

Depth level: 100, iterations: 10000
Throw: 947ms
OperatioResult: 10ms

Depth level: 1, iterations: 100000
Throw: 701ms
OperatioResult: 1ms

Depth level: 10, iterations: 100000
Throw: 1513ms
OperatioResult: 9ms

Depth level: 100, iterations: 100000
Throw: 8049ms
OperatioResult: 92ms
