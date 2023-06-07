# Razor page
- The layout will be applied for every page if it is defined in the `_ViewStart.cshtml`
```c#
@{
 Layout = "∼/Views/_BasicLayout.cshtml";
}
```
- If the page wants to have specific layout, it can achive by setting layout to `null` and the page has to contain its layout, or it can redefine in the razor page.
- `@:html content` you can insert HTML content directly into a C# code block in razor page.
# Pagination
- Example solution:
```C#
public static MvcHtmlString PageLinks(this HtmlHelper html,
                                              PagingInfo pagingInfo,
                                              Func<int, string> pageUrl) {

            StringBuilder result = new StringBuilder();
            for (int i = 1; i <= pagingInfo.TotalPages; i++) {
                TagBuilder tag = new TagBuilder("a");
                tag.MergeAttribute("href", pageUrl(i));
                tag.InnerHtml = i.ToString();
                if (i == pagingInfo.CurrentPage) {
                    tag.AddCssClass("selected");
                    tag.AddCssClass("btn-primary");
                }
                tag.AddCssClass("btn btn-default");
                result.Append(tag.ToString());
            }
            return MvcHtmlString.Create(result.ToString());
        }
// razor page
@Html.PageLinks(Model.PagingInfo, x => Url.Action("List", new { page = x }))
```
# Model binding
- The MVC framework uses a system called model binding to create C# objects from HTTP requests in order to pass them as parameter values to action methods. Model binders can create C# types from any information that is available from the request.
```C#
public class DataModelBinder : IModelBinder
    {
        private const string sessionKey = "Data";

        public object BindModel(ControllerContext controllerContext, ModelBindingContext bindingContext)
        {
            Data data = null;
            if (controllerContext.HttpContext.Session != null)
            {
                data = (Data)controllerContext.HttpContext.Session[sessionKey];
            }
            if (data == null)
            {
                data = new Data();
                if (controllerContext.HttpContext.Session != null)
                {
                    controllerContext.HttpContext.Session[sessionKey] = data;
                }
            }
            return data;
        }
    }
// it must be registered from Global.axax.cs file
ModelBinders.Binders.Add(typeof(Data), new DataModelBinder());
```
# Routes
- It must be registered in the Global.asax.cs file 
- The order of route mapping is based on the order of route appear in the `RouteCollection`.
- Register routes in `RouteConfig.cs`
```C#
// add route with constructor
Route myRoute = new Route("{controller}/{action}", new MvcRouteHandler());
routes.Add("MyRoute", myRoute);

// add route with default value
routes.MapRoute("MyRoute", "{controller}/{action}", new { action = "Index" });

// add route with segment
routes.MapRoute("", "Public/{controller}/{action}",  
				new { controller = "Home", action = "Index" });

// add route with namespace resolution, if there more than 1 controller with same name but different namespace it should be added in a seperate route
routes.MapRoute("MyRoute", "{controller}/{action}/{id}/{*catchall}",  
                new { controller = "Home", action = "Index",  
                    id = UrlParameter.Optional  
                } , new[] { "URLsAndRoutes.AdditionalControllers" });

// add route with constraint
routes.MapRoute("MyRoute", "{controller}/{action}/{id}/{*catchall}",  
               new { controller = "Home", action = "Index", id = UrlParameter.Optional },  
               new {  
                   controller = "^H.*", action = "Index|About",  
                   httpMethod = new HttpMethodConstraint("GET"),  
                   id = new CompoundRouteConstraint(new IRouteConstraint[] {  
                        new AlphaRouteConstraint(),  
                        new MinLengthRouteConstraint(6)  
                    })  
               },  
               new[] { "URLsAndRoutes.Controllers" });
```
- Route can be registered from the controller and action by declearing a attribute routing to them.
```C#
// register this in the RouteConfig.cs to use attribute routing feature 
routes.MapMvcAttributeRoutes();
//
[RoutePrefix("Users")]
public class CustomerController : Controller {

	[Route("~/Test")]
	public ActionResult Index() {
		ViewBag.Controller = "Customer";
		ViewBag.Action = "Index";
		return View("ActionName");
	}

	[Route("Add/{user}/{id:int}")]
	public string Create(string user, int id) {
		return string.Format("Create Method - User: {0}, ID: {1}", user, id);
	}

	public ActionResult List() {
		ViewBag.Controller = "Customer";
		ViewBag.Action = "List";
		return View("ActionName");
	}
}
```
- Generate fully qualified URLs in Links
```C#
@Html.ActionLink("This is an outgoing URL", "Index", "Home",  
            "https", "[myserver.mydomain.com](http://myserver.mydomain.com/)", " myFragmentName",  
            new { id = "MyId" },  
            new { id = "myAnchorID", @class = "myCSSClass" })
// result 
<a class="myCSSClass"  
     href="[https://myserver.mydomain.com/Home/Index/MyId#myFragmentName](https://myserver.mydomain.com/Home/Index/MyId#myFragmentName)"  
     id="myAnchorID">This is an outgoing URL</a>
```
- Generate a URL from a specific route
```C#
// register route
routes.MapRoute("MyOtherRoute", "App/{action}", new { controller = "Home" });
// route link
@Html.RouteLink("Click me", "MyOtherRoute","Index", "Customer")
// result
<a Length="8" href="/App/Index?Length=5">Click me</a>
```
- `GetRouteData (HttpContextBase httpContext)`: This is the mechanism by which _inbound URL matching_ works. The framework calls this method on each `RouteTable.Routes` entry in turn, until one of them returns a non-null value.
- `GetVirtualPath (RequestContext requestContext, RouteValueDictionary values)`: This is the mechanism by which _outbound URL generation_ works. The framework calls this method on each `RouteTable.Routes` entry in turn, until one of them returns a non-null value
# Controller
- Class names that end with base
```C#
// convert back to base class
HttpContext myContext = getOriginalObjectFromSomewhere();  
HttpContextBase myBase = new HttpContextWrapper(myContext);
``` 
- Receiving request data:
	- Extract it from a set of context objects
	- Have the data passed as parameters to your action method
	- Explicitly invoke the framework's model binding feature
![[Pasted image 20230528143020.png]]
- There are build-in value providers that fech items from `Request.Form`, `Request.QueryString`, `Request.Files` and `RouteData.Values`. The values are then passed to model binders that try to map them to the types that your action methods require as parameters.
- When the MVC framework receives an `ActionResult` object from an action method, it calls the `ExecuteResult` method defined by that object. The action result implementation then deals with the `Response` object for you, generating the output that corresponds to your intention.
```C#
public class CustomRedirectResult : ActionResult {  

	public string Url { get; set; }  

	public override void ExecuteResult(ControllerContext context) {  
		string fullUrl = UrlHelper.GenerateContentUrl(Url, context.HttpContext);  
		context.HttpContext.Response.Redirect(fullUrl);  
	}  
}
```
![[Pasted image 20230528152256.png]]
- The `RedirectToAction` (for temporary redirections) or the `RedirectToActionPermanent` (for permanent redirections) are just wrappers around the `RedirectToRoute`.
# Controller factory
![[Pasted image 20230528194306.png]]
- To create a controller factory, the class must inherite the `IControllerFactory`
	- `CreateController()`
	- `GetControllerSessionBehavior()` determine if session data should be maintained for a controller
	- `ReleaseController()` dispose the controller object created by the `CreateController()` is no longer in needed. 
- For most applications, the built-in controller factory class is called `DefaultControllerFactory`. When it receives a request from the routing system, the factory looks at the routing data to find the controlelr property and tries to find a class in the web application that meets the following criteria:
	- Must be public
	- Concrete
	- Must not take generic parameters
	- Name ends with controller
	- Must implement IController
- Custom `DefaultControllerFactory` controller instaniation
	- Using the dependency resolver (DI)
	- Using a controller activator
		- To create a controller invoker the class must inherite the `IActionInvoker`
		- There is the built-in action invoker `ControllerActionInvoker`
- Custom action name can override the action name.
```C#
[ActionName("Enumerate")]  
public ViewResult List() {  
	return View("Result", new Result {  
		ControllerName = "Customer",  
		ActionName = "List"  
	});  
}
```
- Custom action method selector
```C#
public class LocalAttribute : ActionMethodSelectorAttribute
{
	public override bool IsValidForRequest(ControllerContext controllerContext, MethodInfo methodInfo)
	{
		return controllerContext.HttpContext.Request.IsLocal;
	}
}
```
- To handle unknow actions, the controller overrides the `HandleUnknowAction()`
- Improving performance with specialized controllers
	- Using sessionless controllers
	- Using asynchronous controllers
# Templated helper method
- Applying Metadata to a Buddy class
	- It is not always possible to apply metadata to an entiy model class. Example with Entity Framework the class would change if an update applies. The solution to this problem is to use buddy class.
```C#
[MetadataType(typeof(PersonMetaData)]  
public partial class Person {  
	public int PersonId { get; set; }  
	public string FirstName { get; set; }  
	public string LastName { get; set; }  
	public DateTime BirthDate { get; set; }  
	public Address HomeAddress { get; set; }  
	public bool IsApproved { get; set; }  
	public Role Role { get; set; }  
}
  
[DisplayName("New Person")]  
public partial class PersonMetaData {  
	[HiddenInput(DisplayValue=false)]  
	public int PersonId { get; set; }  

	[Display(Name="First")]  
	public string FirstName { get; set; }  

	[Display(Name = "Last")]  
	public string LastName { get; set; }  

	[Display(Name = "Birth Date")]  
	[DataType(DataType.Date)]  
	public DateTime BirthDate { get; set; }  

	[Display(Name="Approved")]  
	public bool IsApproved { get; set; }  
}
```
# Basic links and URLs
![[Pasted image 20230528221532.png]]
# Ajax
- `Request.IsAjaxRequest()` limitations:
	- The method return true if the browser has included the `X-Requested-With` header in its request and set the value `XMLHttpRequest`. This is a widely used convention, but it isn't universal, and so you should consider whether your users are likely to make requests that require JSON data withou settin this header.
	- Your application may be better served by separating the way that a request has been made from the data format that the client seeks.
# Model binder
- Order of DefaultModelBinder class looks for parameter data:
	1. Request.Form
	2. RouteData.Values
	3. Request.QueryString
	4. Request.Files
- Specifying custom prefixes
	- The object binding is complex for MVC framework to understand, to solve this problem use `Bind(Prefix="class")]` in the action method.
	- Binding can be selective by using `Exclude` or `Include` 
```C#
public class Person {
	public int PersonId { get; set; }
	public string FirstName { get; set; }
	public string LastName { get; set; }
	public DateTime BirthDate { get; set; }
	public Address HomeAddress { get; set; }
	public bool IsApproved { get; set; }
	public Role Role { get; set; }
}

public class Address {
	public string Line1 { get; set; }
	public string Line2 { get; set; }
	public string City { get; set; }
	public string PostalCode { get; set; }
	public string Country { get; set; }
}
public class AddressSummary {
	public string City { get; set; }
	public string Country { get; set; }
}
// the view is the form contain Person object to submit
// the controller handles the form submit bind the AddressSummary class
public ActionResult DisplaySummary([Bind(Prefix = "HomeAddress", Exclude = "Country")]AddressSummary summary) {
	return View(summary);
}
```
- Manually invoking model binding: use `UpdateModel()`
```C#
// use try catch
public ActionResult Address(FormCollection formData) {  
    IList<AddressSummary> addresses = new List<AddressSummary>();  
    try {  
        UpdateModel(addresses, formData);  
    } catch (InvalidOperationException ex) {  
        // provide feedback to user  
    }
    return View(addresses);  
}
// use TryUpdateModel
public ActionResult Address(FormCollection formData) {  
    IList<AddressSummary> addresses = new List<AddressSummary>();  
    if (TryUpdateModel(addresses, formData)) {  
        // proceed as normal  
    } else {  
        // provide feedback to user  
    }
    return View(addresses);  
}
```
- Custom value provider
```C#
// Implement IValueProvider
public class CountryValueProvider : IValueProvider {  

	public bool ContainsPrefix(string prefix) {  
		return prefix.ToLower().IndexOf("country") > -1;  
	}  

	public ValueProviderResult GetValue(string key) {  
		if (ContainsPrefix(key)) {  
			return new ValueProviderResult("USA", "USA",  
				CultureInfo.InvariantCulture);  
		} else {  
			return null;  
		}  
	}  
}
// register value provider in Global.asax file
// 0 is register prority precedence 
ValueProviderFactories.Factories.Insert(0, new CustomValueProviderFactory());
```
- Custom model binder
![[Pasted image 20230528231110.png]]
```C#
// implement IModelBinder
public class AddressSummaryBinder : IModelBinder {  

	public object BindModel(ControllerContext controllerContext,  
			ModelBindingContext bindingContext) {  

		AddressSummary model = (AddressSummary)bindingContext.Model  
			?? new AddressSummary();  
		model.City = GetValue(bindingContext, "City");  
		model.Country = GetValue(bindingContext, "Country");  
		return model;  
	}  

	private string GetValue(ModelBindingContext context, string name) {  
		name = (context.ModelName == "" ? "" : context.ModelName + ".") + name;  

		ValueProviderResult result = context.ValueProvider.GetValue(name);  
		if (result == null || result.AttemptedValue == "") {  
			return "<Not Specified>";  
		} else {  
			return (string)result.AttemptedValue;  
		}  
	}  
}
// register in Global.asax
ModelBinders.Binders.Add(typeof(AddressSummary), new AddressSummaryBinder());
// register with an attribute
[ModelBinder(typeof(AddressSummaryBinder))]  
public class AddressSummary {  
	public string City { get; set; }  
	public string Country { get; set; }  
}
```
# Validation
- Defining self-validating methods:
	- `IValidatableObject`
- Enable client side validation:
```C#
// Web.Config
  <add key="ClientValidationEnabled" value="true" />  
  <add key="UnobtrusiveJavaScriptEnabled" value="true" />
```
# Filter
![[Pasted image 20230529162356.png]]
- <mark style="background: #D2B3FFA6;">Authorization filter:</mark>
	- `IAuthorizationFilter`
	- `AuthorizeAttribute`
- <mark style="background: #D2B3FFA6;">Authentication filter:</mark>
	- Authentication filters have a relatively complex lifecycle. They are run before any other filter, which lets you define an authentication policy that will be applied before any other type of filter is used. Authentication filters can also be combined with authorization filters to provide authentication challenges for requests that don’t comply to the authorization policy. Authentication filters are also run after an action method has been executed but before the `ActionResult` is processed.
	- `IAuthenticationFilter`
		- `OnAuthentication` the controller will call the `OnAuthentication` method before running any other kind of filter providing an opportunity to perform a broad check.
		- `OnAuthenticationChanllenge` is called whenever a request has failed the authentication or authorization policies for an action method. If request is not fail it will be call before `ActionResult` is processed.
	- `FilterAttribute`
- <mark style="background: #D2B3FFA6;">Exception filter</mark>
- <mark style="background: #D2B3FFA6;">Action filter</mark>
# ASP.NET life cycles
- <mark style="background: #D2B3FFA6;">Application life cycle</mark> tracks the life of a web application from the moment it starts to the moment it is terminated.
	- ASP.NET provides notifications when the application starts and stops.
	- Defined by the global application class which is the Global.asax and Global.asax.cs file
	- `Application_Start()` is called when the application started.
	- `Application_End()` is called when the application is about to be terminated, and it is an opportunity to release any resource that the application maintains.
	- `System.Web.HttpApplication` which is the base class for the application, and it is not define the method, it uses reflection to look for the methods by name.
- <mark style="background: #D2B3FFA6;">Request life cycle</mark> defines the path that an HTTP request follows as it moves through the ASP.NET platform from the point at which the initial request is received until the response is sent.
- ASP.NET life cycles
![[Pasted image 20230530205638.png]]
![[Pasted image 20230530205646.png]]
- HttpContext
![[Pasted image 20230530211704.png]]
![[Pasted image 20230530211714.png]]
- ASP.NET context objects provide information about the application, the current request, and the response that is being prepared for it, they also provide access to the most important ASP.NET platform services such as security and state data.
- `HttpApplication` objects describes a single HTTP requests as it is being processed.
# IHttpModule
- Modules are classes that handle life-cycle events to monitor or manipulate requests or responses. Modules can also provide services to handlers.
- Methods defined
	- `Init(app)`
	- `Dispose()`
- Create self-register modules: Using an assembly attribute called `PreApplicationStartMethod`.
- These modules can access from `HttpContext.ApplicationInstance.Modules`
# IHttpHandler
- Handlers are the ASP.NET request handling component that is responsible for generating the response content for requests
  ![[Pasted image 20230605204152.png]]
  ![[Pasted image 20230605204202.png]]
  - Methods defined
	  - `ProcressRequest(context)`
	  - `IsReusable()`
  - Request life-cycle events relevant to handlers
	  - `MapRequestHandler`: locate a handler for the request
	  - `PostMapRequestHandler`: locate a handler for the request
	  - `PreRequestHandlerExecute`:  trigger immediately before the `ProcessRequest()` called
	  - `PostRequestHandlerExecute`: trigger immediately after the `ProcessRequest()` called
  - `IRouteHandler` map the route with the `IHttppHandler`
```C#
// how to use IHttpModule with IHttpHandler
namespace SimpleApp.Infrastructure {
  public class DayModule: IHttpModule {
    public void Init(HttpApplication app) {
      app.PostMapRequestHandler += (src, args) => {
        if (app.Context.Handler is DayOfWeekHandler) {
          app.Context.Items["DayModule_Time"] = DateTime.Now;
        }
      };
    }
    public void Dispose() {
      // nothing to do
    }
  }
}
```
- `IHttpHandlerFactory` is responsible for providing instances of handler classes to the ASP.NET platform in order to generate content for requests
	- Methods defined
		- `GetHandler(context, verb, url, path)`
		- `ReleaseHandler(handler)`
# Disrupting the request life cycle
- Redirect URL with handler.
- Select handler to disrup.
- Transfer a request to a different handler using `TransferRequest()`.
- Terminating requests using `CompleteRequest()`
- Add handle event method with delegation
```C#
public class EventListModule : IHttpModule {

        public void Init(HttpApplication app) {

            string[] events = { "BeginRequest", "AuthenticateRequest",
                "PostAuthenticateRequest", "AuthorizeRequest", "ResolveRequestCache",
                "PostResolveRequestCache", "MapRequestHandler", "PostMapRequestHandler",
                "AcquireRequestState", "PostAcquireRequestState", 
                "PreRequestHandlerExecute", "PostRequestHandlerExecute", 
                 "ReleaseRequestState", "PostReleaseRequestState",
                "UpdateRequestCache", "LogRequest", "PostLogRequest",
                "EndRequest", "PreSendRequestHeaders", "PreSendRequestContent"};


            MethodInfo methodInfo = GetType().GetMethod("HandleEvent");
            foreach (string name in events) {
                EventInfo evInfo = app.GetType().GetEvent(name);

                evInfo.AddEventHandler(app, 
                    Delegate.CreateDelegate(evInfo.EventHandlerType,
                        this, methodInfo));
            }

            app.Error += (src, args) => {
                System.Diagnostics.Debug.WriteLine("Event: Error");
            };
        }

        public void HandleEvent(object src, EventArgs args) {
            string name = HttpContext.Current.CurrentNotification.ToString();
            if (HttpContext.Current.IsPostNotification && 
                !HttpContext.Current.Request
                    .CurrentExecutionFilePathExtension.Equals("css")) {
                name = "Post" + name;
            }
            if (name == "BeginRequest") {
                System.Diagnostics.Debug.WriteLine("---------------");
            }
            System.Diagnostics.Debug.WriteLine("Event: {0}", new string[] { name });
        }

        public void Dispose() {
            // do nothing
        }
    }
```
# Trace requests
- Use `Trace.axd` to see logged traces.
- Use handler to write trace with events.
- Use third-party library like `Glimpse`.
# Configuration
![[Pasted image 20230605221036.png]]
- Reading from config file: use `WebConfigurationManager.AppSettings` and access it like a dictionary.
# Application state data
- Application state data allows small amounts of data to be shared throughout an application and is available to every component.
- It is available through `HttpContext.Application`
- When working on Application state data, we should care about the synchronization effect
![[Pasted image 20230605223138.png]]
![[Pasted image 20230605223148.png]]
- Tracking sessions without cookies
![[Pasted image 20230605223415.png]]
# Donut and donut-hole caching
- The content from an action method is cached by the server and supplemented by calls to action methods in every request. The name arises because the part that is cached (the donut) entirely surrounds the parts that are not (the donut holes). The donut is the content from the action method, and the donut holes are the content from the child actions.
- Donut caching can be useful when the skeletal structure of the content is static and needs to be blended with fragments of dynamic content.
