# school-test-app-solution
### TODO 0
There was no dependency injection for AccountService, so I added a singleton in ConfigureServices():
```csharp
services.AddSingleton<IAccountService, AccountService>();
```
### TODO 1
For Cookie Authentication I needed first to make changes in Startup.cs:
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
Then in LoginController.Login I added the following piece of code:
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
The list of `Claim` entities stores information about the identifier and the roles of users. All authentication information will be stored in cookies because it was set as a default authentication method.
### TODO 2
I added the following piece of code that sets Response.StatusCode to 404 if user not found:
```csharp
else
{
    Response.StatusCode = 404;
}
```
### TODO 3
The external ID can be received from the User object:
```csharp
return _accountService.LoadOrCreateAsync(User.Identity.Name);
```
### TODO 4
I had to configure options in AddCookie() method to make the app return 401 for unauthorized users:
```csharp
 services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options =>
                {
                    options.Events.OnRedirectToLogin = context =>
                    {
                        context.Response.StatusCode = 401;
                        return Task.CompletedTask;
                    };
                    options.Events.OnRedirectToReturnUrl = context =>
                    {
                        context.Response.StatusCode = 401;
                        return Task.CompletedTask;
                    };
                    options.Events.OnRedirectToAccessDenied = context =>
                    {
                        context.Response.StatusCode = 401;
                        return Task.CompletedTask;
                    };
                });
```
### TODO 5
The `[Authorize]` attribute was modified to limit access only to users who have `Admin` role:
```csharp
[Authorize(Roles = "Admin")]
```
### Code
To see the code you should switch to master branch.
