# school-test-app-solution
#### TODO 0
There was no dependency injection for AccountService, so I added a singleton in ConfigureServices().
```csharp
services.AddSingleton<IAccountService, AccountService>();
```
