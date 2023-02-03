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
Let's say that we want to implement the RegisterUser feature.
And we have some base code:
class OperationResult
{
    public bool IsSuccess { get; private set; }
    public static OperationResult Success => new OperationResult { IsSuccess = true };
    public static OperationResult Failure => new OperationResult { IsSuccess = false };
}

interface IAvatarRepository
{
    Uri Upload(string avatar);
}

interface IUserRepository
{
    object Get(string domainName);
    object Add(object user);
}

interface ITenantRepository
{
    object Get(int id);
    void Add(object user);
    void AssignUser(int id, object user);
}

class RegisterUserCommand
{
    public string DomainName { get; set; }
    public string UserName { get; set; }
    public string Avatar { get; set; }
    public int TenantId { get; set; }
}

Case 1: Exception throwing

And we execute this in some API controllers. We are about to return the correct HTTP status codes: 200/400/401/404/500/409 etc.

We can create a global API exception handler and return the correct ActionResult based on the caught exception type.
For example, NotFoundException means -> 404. BadRequestException means -> 400 etc. All other -> 500.

Great. It will work.
But what if, for example, failure with avatar saving shouldn't cause the failure of the whole operation? We don't care about the avatar. But we have to save the user.

What can we do? 
We can add try-catch into the command handler!

But wait. What kind of exception should we catch? All of them?
