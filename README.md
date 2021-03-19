# API authentication with ASP.NET 5 and Firebase

Here are the steps to secure your API with Firebase.

- Install packages (NuGet): **Microsoft.AspNetCore.Authentication.JwtBearer**
- Update your Controllers to add **[Authorize]** attribute to your class or method.
- Update the Startup class with the following add-ons.
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Authority = "https://securetoken.google.com/my-project-id";
            options.TokenValidationParameters = new TokenValidationParameters
            {
                ValidateIssuer = true,
                ValidIssuer = "https://securetoken.google.com/my-project-id",
                ValidateAudience = true,
                ValidAudience = "my-project-id",
                ValidateLifetime = true
            };
        });
      services.AddControllers();
}
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    app.UseHttpsRedirection();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
 }
 ```
## Create a token
To do a log-in with email/password (the ones we created manually before), the URL for the POST request is https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=[API_KEY] and here is an example of request body (obviously change the email and password).
```json
{
  "email": "myemail@address.changeme",
  "password": "mysecretpassword",
  "returnSecureToken": "true"
}
```

You should have a successful reply, assuming you provided with a valid token (limited in time) in the idToken field.

```json
{
  "kind": "identitytoolkit#VerifyPasswordResponse",
  "localId": "****",
  "email": "****",
  "displayName": "",
  "idToken": "****",
  "registered": true,
  "refreshToken": "****",
  "expiresIn": "3600"
}
```

## Secure call to the API
Here it is, the final step, you have everything to validate your API!

You can use Postman or any other web request tool. Or you can look at the code sample on how to add swagger to your API (a little more code but a little more fun 😎).

*If you’re not familiar with JSON Web Token (JWT) there are many resources on the web but you can start by this website: [jwt.io](https://jwt.io/).*

The very important information to know is the token is the key element that will secure the calls to the API and will give the user authentication information to the API (you can look at it in debug in Controllers through the **User** property).

In the REST call this token is located in the request Headers fields, called Authentication, with the value “Bearer xxxxx” where xxxx is the token.
