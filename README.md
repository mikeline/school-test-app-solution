# school-test-app-solution
### TODO 0
There was no dependency injection for AccountService, so I added a singleton in ConfigureServices().
```csharp
services.AddSingleton<IAccountService, AccountService>();
```
### TODO 1
For Cookie Authentication I needed first to make changes in Startup.cs
##### ConfigureServices(...)
```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(...);
services.AddAuthorization();
```
##### Configure(...)
```csharp
app.UseAuthentication();
app.UseAuthorization();
```
Then in LoginController.Login I added the following piece of code
```csharp
var claims = new List<Claim>
{
    new Claim(ClaimsIdentity.DefaultNameClaimType, account.ExternalId),
    new Claim(ClaimsIdentity.DefaultRoleClaimType, account.Role)
};

var claimsIdentity = new ClaimsIdentity(
        claims, "ApplicationCookie", ClaimsIdentity.DefaultNameClaimType, ClaimsIdentity.DefaultRoleClaimType);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```
`claims` store information about the identifier and the roles of users. All authentication information will be stored in cookies because it was set as a default authentication method.
