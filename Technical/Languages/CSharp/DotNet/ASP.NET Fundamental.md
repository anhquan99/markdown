# Access modifier

- `public`
- `private`
- `protected`
- `internal`: can be accessed by the same assembly
- `protected internal` : the type or member can be accessed by any code in the assembly in which it's declared, or from within a derived `class` in another assembly
- `private protected`: can be accessed by types derived from the `class` that are declared within its containing assembly
# Dependency injection

- Registration of the dependency in a service container. ASP.NET Core provides a built-in service container, `IServiceProvider`. Services are typically registered in the app's `Program.cs` file.
- Injection of the service into the constructor of the class where it's used. The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.
- The framework creates a scope per request, and `RequestServices` exposes the scoped service provider. All scoped services are valid for as long as the request is active.
- By default, Entity Framework contexts are added to the service container using the scope lifetime because web app database operations are normally scoped to the client request.
- If we register many dependency injection in `IServiceProvider` with the same interface, class and different, the last register will be registered when project start the rest will be ignored.

# Middleware

- Middleware is software that's assembled into an app pipeline to handle requests and responses. Each component
- Chooses whether to pass the request to the next component in the pipeline
- Can perform work before and after the next component in the pipeline
- Request delegates are used to build the request pipeline. The request delegates handle each HTTP request
- Each delegate can perform operations before and after the next delegate. Exception handling delegates should be called early in the pipeline so they can catch exceptions that occur in later stages of the pipeline.
- When a delegate doesn't pass a request to the next delegate, it's called **short-circuiting the request pipeline**. It is often desirable because it avoid unnecessary work.

```ad-note
Don't call `next.Invoke` after the response as been sent to the client.Changes to `HttpResponse` after the response has started throw an exception.
```

- The order that middleware components are added in the `Program.cs` file defines the order in which the middleware components are invoked on requests and the reverse order for the response. The order is critical for security, performance and functionality.

``````csharp
// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment()) {
     app.UseMigrationsEndPoint();
}
else {
     app.UseExceptionHandler("/Error");
     // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
     app.UseHsts(); } app.UseHttpsRedirection();
     app.UseStaticFiles();
     // app.UseCookiePolicy();
     app.UseRouting();
     // app.UseRequestLocalization();
     // app.UseCors();
     app.UseAuthentication();
     app.UseAuthorization();

     // app.UseSession();
     // app.UseResponseCompression();
     // app.UseResponseCaching();

     app.MapRazorPages();
     app.MapControllerRoute( name: "default", pattern: "{controller=Home}/{action=Index}/{id?}");

     app.Run();

``````

- Middleware not necessarily appears in exact order but:
     - `UseCors`, `UseAuthentication` and `UseAuthorization` must appear in the order shown
     - `UseCors` must appear before `UseResponseCaching`
     - `UseRequestLocalization` must appear before any middleware that might check the request culture. (ex: `UseMvcWithDefaultRoute()`)
# Host
- A host is an object that encapsulates an app's resources. When a host starts, it calls `IHostedService.StartAsync` on each implementation of `IHostedService` registered in the service container's collection of hosted services