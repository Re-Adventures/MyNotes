# ASP .NET MVC

* ASP.NET MVC is now a legacy software & is not updated my Microsoft

* To create a MVC Project, we have to select the project template to be "ASP.NET Web Application (.NET Framework)" in Visual Studio, & in the next page select MVC template & click on create

# Structure of the project

1.  App_Data - here the database file is stored
2.  App_Start - Contains the scripts which are called at the start of the application
3.  Content - Contains images, css file, etc.
4.  Controllers - Contains the controllers
5.  fonts - font files, example .tff files
6.  Models - all the domain classes will exist here
7.  Scripts - all javascript files are placed here
8.  Views - Contains the web pages
9.  Global.asax - We can define event handlers here, which will be called automatically
10. packages.config - used by NuGet Package Manager
11. Web.config - Configuration for web app

* In whatever folder the view is present, its equivalent named controller will be called, i.e. Views/Home/samplePage.html will have its equvialent controller as /Controller/HomeController.cs
* There is also a Shared folder in the Views directory, this contains views that can be called across multiple controllers

# Creating the web page

* First on the Models folder, Add Item & select class file. Inside that file, it will contain an empty class, here we need to add members to the class
* Now in the Controllers folder, add a new controller which will have the same name as the model file. First start by importing the Models file by adding the following line
using ProjectName.Models
* Now we can create objects from the model file & return the model object using View(movieObj)
* In the Views folder, there will be already be a directory created for the model which has the same name as the Model file, now create a new view inside that folder using Add -> View
* Opening that view, we can see a mixture of html & C# on the page. C# code is usually inside of @{ ... }. To use the models, we need to add @model ProjectName.Models.Model_Name, & then we can access fields inside of the model, ex \<h2>@Model.field1 @Model.field2\</h2>

# ActionResult

* Most of the controllers we have will have the return type as ActionResult, it is basically a collection which can accepts several Result types like ViewResult, JsonResult, HttpNotFoundResult, EmptyResult, etc.

1. ContentResult
* ContentResult defines that the return type of the function will be from Content().
* Using Content("Some String"), will simply return the "Some String" back to the user & will be displayed as a plain string instead of HTML content

2. HttpNotFoundResult
* Expects the return type should be from HttpNotFound(), opens the 404 error page on browser

3. HttpStatusCodeResult(HttpStatusCode, Description);

# Routing

## Convention based routing (the old way)

* In the RouteConfig.cs file, there is already a Default route defined which has the url set to be "{controller}/{action}/{id}", i.e it will match urls of the form domain-name/Home/GetRandom/1, but what if we want to have the url of the form domain-name/Pages/2023/05, then it will not match the url format & will throw a 404 not found error. To fix this issue, we can define another MapRoute above the Default one (just to have more priority) like so
```c#
  route.MapRoute(
    name: "MyRoute", // Unique name given to the router
    url:  "{controller}/{action}/{year}/{month}" // If url follows this format then use this route
    defaults: new {controller = "Home", action = "GetRandom", year = UrlParam.Optional, ...}, // default values if not passed
  )
```

* We can also add a 4th parameter `constraints` in which we can pass a regex to only accept parameters which follow that regex
```c#
  constraints: new { year = @"(2020|2021)", month = @"\d{2}" }
```

## Attribute Routing (new way)

* We need to enable this feature by calling the method routes.MapMvcAttributesRoutes() in the RouteConfig.cs file
* Now we can just go to our controller method & on top of that we can add an Attribute like so
```c#
[Route("Movies/released/{year:regex(\\d{4})}/{month:regex(\\d{2}):range(1, 12)}")]
public ActionResult MyMethod()
{
  ...
}
```

# Views

## View Model

* In the view page, we can only access one model, but what if we want to access multiple models which are sent from the backend, in these cases we use view models.
* For creating a View Model, create a folder in our project called as `ViewModel` & inside of that create a c# class file with name as somethingViewModel.cs (can be anything, just used as a convention)
* Now in the ViewModel class file we can add multiple fields for the view model class like so
```c#
public class RandomMovieViewModel
{
    public Movie Movie { get; set; }              // Model 1
    public List<Customer> Customers { get; set; } // Model 2
}
```

* Now in our Controller action method, we can instantiate this class & pass this viewmodel to the View() instead of a single model
* Finally, we can use this in out web page like so
```c#
<h2>@Model.Movie.Name</h2>
if (somecondition) {
  @foreach(var customer in Model.Customers) {
    <li>@customer.name</li>
  }
}
else {
  <p>No Data Found</p>
}
```

## Partial Views

* Partial view is just a template which can be reused in other pages. These are stored in Views/Shared.
* One example is the \_Layout.cshtml, which contains the basic layout of the page, inside of that page, we can see a call to RenderBody(), what this function does is that it will copy the contents of whatever page is using this template & replace it with the contents of that page, i.e if asdf.cshtml is using this \_Layout template, when RenderBody() is called, it will copy all contents of asdf.cshtml & place where the RenderBody function is called
* We can create our own Partial Views which are called by out main \_Layout page, this can be done by RightClicking on Views/Shared -> Add -> View, add the name as \_MyLayout.cshtml & make sure to check the "Create partial view" checkbox so that it will not inherit other partial views. Now back in our \_Layout file, add the following line whereever we want the partial view to be used
```c#
  @Html.Partial("_MyLayout", [optional]someModel);
```

# Working with data

## Entity Framework

* It is a object relational mapper(ORM) which maps data from the database into objects in our application. In simple words, it is a tool used to access a DataBase.
* In ORM there are two terminologies, DbContext & DbSet, DbContext is basically a gateway to our database tables & DbSet are our tables
* There are two workflows to use Entity Framework:
  1. Database First: design db tables first & then let Entity Framework generate domain classes
  2. Code First: create our domain classes first & then let Entity Framework generate DB for us
* In this we are only going to use `Code First` workflow.

## Code First Migrations

* Whenever we make changes to our domain classes, we should create migrations, migrations is similar to a version control. It saves the current state to db before modifying & if we face any problems, we can just revert back to a migration.
* Before using migrations, we need to first enable the migrations, which can be done by opening the Nuget Package Manager console & running `Enable-Migrations`.
* If `Enable-Migrations` is not recognized as a command, then we need to install a package for that using `Install-Package EntityFramework -IncludePrerelease`
* After enabling the migrations, a directory will be created into our solution named as `Migrations`. Here all the migrations will be stored.
* If the `Enable-Migrations` command fails with `No context type was found in the assembly 'SampleWebApplication'` then we need to create our own DBContext. For that we need to create a file in our Models folder with the following contents
```c#
using System.Data.Entity;

namespace SampleWebApplication.Models
{
    public class MyDBContext : DbContext
    {
        public MyDBContext()
        {
        }
        public DbSet<Customers> Customers { get; set; }
    }
}
```
* For creating a migration, we need to run `Add-Migration MigrationName`, for ex `Add-Migration InitialModel`
* After adding the migration, a cs file will be created in the Migrations folder.
* On opening that file, we can see a lot of DB table creation queries, for ex 'AspNetRoles', 'AspNetUsers', etc. These tables are part of asp.net identity model used for authentication.
* In the `...Models.cs` file, add our own model so that the manager is aware of our domain classes & will generate db accordingly
```c#
    public DbSet<Customers> Customers { get; set; }
```
* Finally run the `Update-Database` command to create the database (.mdf file) in the `App_Data` folder, which basically runs the current Migration file
* On double clicking the .mdf file, it will open the Server Explorer window where we can view our database created
* By default, the database columns will be generated according to c# datatypes, for example string -> NVARCHAR(MAX) NULL, we can change the behaviour by using the following
```c#
using System.ComponentModel.DataAnnotations;
...
[Required]            // Ensures that it should not bu nullable
[StringLength(1000)]  // Maximum length of the string which it can hold
public string UserName {get; set;}
```

## Seeding the Database

* Sometimes we want to have consistent data in the DB whenever it is deployed, for example in cases like Membership type, we might want to populate the DB with the same values whenever we deploy it on the server, for that we use seeding
* For seeding the DB, we need to first add a migration, then in the Up() of the migration file, we can add our sql queries to add data into the DB.
```c#
public partial class InsertDummyValuesToDB : DbMigration
{
    public override void Up()
    {
        // The Sql method runs the sql query provided as an argument to it
        Sql("INSERT INTO MemberShipTypes(Id, SignupFee, Discount, DurationInMonths) VALUES (1, 0, 0, 0)");
        Sql("INSERT INTO MemberShipTypes(Id, SignupFee, Discount, DurationInMonths) VALUES (2, 1000, 5, 1)");
        Sql("INSERT INTO MemberShipTypes(Id, SignupFee, Discount, DurationInMonths) VALUES (3, 2500, 10, 6)");
        Sql("INSERT INTO MemberShipTypes(Id, SignupFee, Discount, DurationInMonths) VALUES (4, 10000, 20, 12)");
    }
}
```

## Querying the DB

* For querying the DB, we first need a context to the DB, which needs to be initialized in the constructor of our controller method
```c#
using 
...
public class CustomersController : Controller
{
    private ApplicationDbContext _context;
    public CustomersController()
    {
        // Initializing the context in the constructor method
        _context = new ApplicationDbContext();
    }

    // For disposing the db object, we need to override the default dispose method
    protected override void Dispose(bool disposing)
    {
        // We need to manually dispose off the db context object
        _context.Dispose();
    }
}
```

* Note: if we get an error that ApplicationDbContext is not found, this usually means that we created our own dbcontext, in the models folder, & we need to replace it with our own db context name
```c#
    public MyDbContext _context;
```

* Now to get the data from the db, we can just use `_context.Customers` to fetch all the records from the DB
```c#
    // This loads the data lazily (also known as deferred execution)
    // it will return, lets say, the pointer to the dbData & will only, fetch the records when we iterate over it
    var customers = _context.Customers;

    // Or we can immediately fetch the records by using methods like .ToList()
    // _context.Customers.ToList();
    return View(customers);
```

## Eager Loading

* If we want to display the customer's membership type, i.e. fetch records from second table using the first table (join), we cannot do that directly.
* By default if we use the `_context.Customers`, it will only load the Customers table & not the MembershipType table.
* To also include it's relational tables, we need to use the `Include` method, which is from the System.Data.Entity package
```c#
    customers = _context.Customers.Include(c => c.MembershipType);
```

# Creating forms

* Before creating forms, we need to return the model from its relevant controller action
```c#
    public ActionResult AddCustomer()
    {
        return View(new Customers());
    }
```
* For creating forms, we can use the `@Html.BeginForm("Action", "Controller")` method. This method adds a `<form>` tag at the start
* For adding the form closing tag, we need to dispose this object, we can do that by wrapping it in a using block
```c#
@using(Html.BeginForm("action", "controller"))
{
  ...
}
```
* To create responsive forms, we need to use div with the class to be `form-group`.
* We can create the form fields using html elements like `<label>` & `<input>`, or we can use the following methods
```c#
  @Html.LabelFor(m => m.Name)
  @Html.TextBoxFor(m => m.Name, new { @placeholder = "Enter Username", @class = "form-control" })
```

* Creating drop down lists is a little complex procedure. We need to use the SelectList method.
* The syntax of the SelectList is as follows
```c#
SelectList(
    Model.SomeList,   // Requires a list of Model. Elements of this list would be used as options for dropdown
    "Id"              // The value of that dropdown option element. Id is a field in the Model.SomeList
    "TypeName"        // The display name of the option element. Typename is also a field in Model.SomeList
)
```

* Now this SelectList can be passed to `DropDownListFor` method
```c#
@Html.DropDownListFor(
    m => m.MembershipTypeId,  // Property name
    new SelectList(Model.MembershipTypes, "Id", "MembershipTypeName"),
    "Select MembershipType",  // Default selected value
    new { }                   // Html properties
)
```

* The first argument to SelectList is a list of model which is sent from the controller of the form \_context.MembershipTypes.ToList(),
which means that the View Model that we will use here should have the field MembershipTypes to be a List<MembershipTypes>

* For creating the backend API to handle the form submission, we can define the method in our controller
```c#
[HttpPost]
public ActionResult CreateUser(NewCustomerViewModel viewModel)
{
    ...
}
```

* Instead of accepting a View Model, we can just accept indiviual models of the View Model
```c#
[HttpPost]
public ActionResult CreateUser(Customers customer)
{
    ...
}
```

* To insert this submitted data to the DB, we need to add this object to our context & finally call `SaveChanges`
```c#
[HttpPost]
public ActionResult CreateUser(Customers details)
{
    _context.Customers.Add(details);
    _context.SaveChanges();
}
```

* To update a record which is in the DB, we need to first fetch the specific record from the context, update the specific records & finally call SaveChanges();
```c#
public ActionResult UpdateUser(Customers updationDetails)
{
    Customers customer = _context.Customers.SingleOrDefault(c => c.id == updationDetails.id)
    
    customer.Username = updationDetails.Username;
    ...
    _context.SaveChanges();
}
```

## Applying validation on the input fields

### Required Validation

* We can apply the required validation on an input field by specifying the `[Required]` on a specific member of the domain class
* Note: By default types such as `int`, `byte`, etc will be required by default, to make it optionally required, we could change their types to be `int?`, `byte?`
```c#
    [Required(ErrorMessage = "Please provide a user name")]
    public string Username
```

* If we dont specify the ErrorMessage field in the Required field, asp .net will use a default message instead
* To display the error message defined in the required field, we need to add validation message in UI using
```c#
    @Html.ValidationMessageFor(m => m.Customer.Username)
```

* If for some reason the validation message is not displayed & the form submission calls the backend API, then we need to include the following two files
```c#
    @Scripts.Render("~/Scripts/jquery.validate.min.js")
    @Scripts.Render("~/Scripts/jquery.validate.unobtrusive.min.js")
```

### Custom Validation

* We can create our own validation on the form fields
* For that we need to create a class file, which can be saved in the models directory. Lets call that `Min18YearsOld`
* This class should derive from `ValidationAttribute` class which is present in the `System.ComponentModel.DataAnnotations`
* Inside of this class, we need to override `IsValid` method
```c#
...
using System.ComponentModel.DataAnnotations;

namespace ...
{
    public class Min18YearsOld : ValidationAttribute
    {
        protected ovverride ValidationResult IsValid(object value, ValidationContext validationContext)
        {
            // `validationContext.ObjectInstance` returns the containing class, so we need to cast it to the model class
            Customers customer = (Customers)validationContext.ObjectInstance;

            if(DateTime.Today.Year - customer.birthDate >= 18)
            {
                // Success Result
                return ValidationResult.Success;
            }
            else
            {
                // Error Result
                return new ValidationResult("Customer should be atleast 18 years old");
            }
        }
    }
}
```

* After creating the custom validation class, we need to apply it to our model field

```c#
[Min18YearsOld]
public DateTime? BirthDate { get; set; }
```

* Finally we need to add the validation message on the frontend
```c#
    @Html.ValidationMessageFor(m => m.Customer.BirthDate)
```

### Anti Forgery Tokens

* To prevent forging of requests, we can use the built-in `@Html.AntiForgeryToken()` in our forms to generate a CSRF token
* On the controller, we can just apply the `ValidateAntiForgeryToken` attribute on our method & it will validate the token automatically


# RESTful APIs

* REST - Representational State Transfer
* According to REST, HTTP method used define the behaviour of the API
1. GET    : Fetching data
2. POST   : Creating data
3. DELETE : Deleting data
4. PUT    : Updating data

* Post requests, which create data, should return 201 (Created) status code & should also return the URI of the newly created data

## Creating API

* In the controllers folder, create a folder named as `Api`
* Inside of `Api`, create a controller `CustomersController` by using the `WebAPI 2 Controller - Empty`
* Now open the `Global.asax.cs` file & add the following line

```c#
using System.Web.Http;
...
protected void Application_Start()
{
    GlobalConfiguration.Configure(WebApiConfig.Register);
    ...
}
```

* Now we can create our APIs in the CustomersController & can be accessed using the url "http://.../api/customers"

* API Example
```c#
[HttpGet]
public Customer Customers(int id)
{
    Customer customer = _context.Customers.SingleOrDefault(elem => elem.Id == id);

    if(customer == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }

    return customer;
}

```

* All the APIs will be called using the same url, only the HTTP method will change
* i.e, even if we have a method in our API controller named as `CreateCustomer`, we will still call the api using `/api/customers/`, with the method as post
* The reason is that, if we look in the `App_Start` folder, there will be a file `WebApiConfig.cs` created automatically & the default route mapping is of the form
```c#
    config.Routes.MapHttpRoute(
        name: "DefaultApi",
        routetemplate: "api/{controller}/{id}",
        defaults: new { id = RouteParameter.Optional }
    );
```

* We can return the error codes back to user by using HttpResponseException
```c#
    if(customer == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
```

* `Model.IsValid` is used to verify if the data sent by the user matches our Model & its validation properties, i.e. it checks if the Razor engine was able to deserialize the data correctly

## Changing the format of parameter names

* By default, the data sent by our API will use the PascalCasing, for example "IsSubscribedToNewsletter", for json keys, but what if we want to change this format to use camelCasing
* For that we need to add the following lines in the `WebApiConfig.cs` file in the `Register` method.

```c#
using Newtonsoft.Json;
using Newtonsoft.Json.Serialization;
...

    var settings = config.Formatters.JsonFormatter.SerializerSettings;
    settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
    setting.Formatting = Formatting.Indented;
```

* Now our api will return json keys with camelCase which is the default casing in javascript

## IHttpActionResult

* Instead of returning a class object, or some primitive types, we can return `IHttpActionResult` from our APIs which is similar to ActionResult.

```c#
public IHttpActionResult CreateUser(User user)
{
    if(!ModelState.IsValid)
    {
        return BadRequest();
    }

    ...

    string url = new Uri($"{Request.RequestUri}/{user.Id}");
    // Status code: 201
    return Created(
        url,  // This sets the `location` header in the HTTP response
        user  // The actual data which will be returned to the caller
    );
}
```

* Sometimes we will get a 404 error on calling some APIs, for fixing that, try adding the property `runAllManagedModulesForAllRequests="true"` to the `<modules>` in the `web.config` file
```xml
    <modules runAllManagedModulesForAllRequests="true">
        <remove name="FormsAuthentication" />
    </modules>
```
## Single Page Application

* Single Page Applications (SPAs) are applications which are generated purely on the client side & only calling the backend API for fetching some data
* i.e. skipping the Razor engine (i.e. no c# code in html pages)
* This is used in out `Token_auth` web application, where there is only a single page & we show/hide tabs according to user clicks

# Authentication & Authorization

## MVC Authentications

1. No Authentication: Anyone can access this application
2. Individual Accounts: Users can register themselves with a form & only users which have an account in this application can login into this application
3. Orgnizational Accounts: Using SSO & Active Directory
4. Windows Authentication: Attached to windows login, when a user unlocks their personal device, it will autmatically be authenticated into this application as well

* For this we are going to use the Individual accounts authentication
* By Default, ASP.NET has implemented the Registration & Login functionality for us. This is part of `ASP.NET Identity` Framework.
* The following DLLs are required by ASP.NET Identity Framework
  1. Microsoft.AspNet.Identity.Core
  2. Microsoft.AspNet.Identity.EntityFramework
  3. Microsoft.AspNet.Identity.Owin

* In our Models -> IdentityModels.cs, we can see a class `ApplicationUser` which derives from `IdentityUser` & `ApplicationDbContext` which derives from `IdentityDbContext`
* Both IdentityUser & IdentityDbContext are a part of the Identity framework
* By default, we would have a `AccountController.cs`, which have the functionality for register, login, etc. implemented for us
* In the register method, we can see the following line
```c#
    var result = await UserManager.CreateAsync(user, model.Password);
```

* The UserManager is an API which makes working with Users object easy. It provides methods like Creating, fetching, deleting, etc.

## Restricting access

* We can use the `Authorize` attribute on our actions to allow access to it only to the users which are logged in
* We can also apply the Authorize attribute to the controller itself, which is same as applying it to each & every action of that controller.
* We can also apply this attribute globally, so that all the controller & actions will apply it
* For applying the Authorize attribute globally, we need to add a filter rule in the App_Start -> FilterConfig.cs

```c#
public static void RegisterGlobalFilters(GlobalFilterCollection filters)
{
    ...
    filters.Add(new AuthorizeAttribute());
}
```

* To allow users to access an action without login, we can apply the `AllowAnonymous` attribute to our action/controller

## Seeding Users & Roles

* So, there is another method to perform the mentioned steps by overriding the `Seed()` of our migration, but apparently this works good on the development side, but when we are converting this to production side, the seed method will not be run & we would have to do that manually after updating the `web.config`, which takes even more effort every time we deploy the application.
* For a slightly better version, check the `Creating Roles using Code` Section

### Creating Dummy Accounts

* First go to the login page, & create a dummy user account. This account will have minimum priviledges
* Now we need to create an Admin user, which will have all the priviledges
* For that go to the Register action in the Account controller & add the following code into the `if(result.Succeeded)` block

```c#
    var roleStore = new RoleStore<IdentityRole>(new ApplicationDbContext());
    var roleManager = new RoleManager<IdentityRole>(roleStore);
    await roleManager.CreateAsync(new IdentityRole("SomeRoleNameHere"));
    // Ex: roleManager.CreateAsync(new IdentityRole("CanManageBooks"));

    await UserManager.AddToRoleAsync(user.Id, "SameRoleSpecifiedAbove");
```

* Now register another user, after registering, REMOVE the above code that we just wrote
* Now we have two users, `guest` & `admin` user in out DB

### Seeding the table

* For seeding the table, we are going to need a migration in which we can add our queries
* First we would need to extract the SQL queries of the created users & their roles.
* For that, open the `SQL Server Object Explorer` tab & inside of that find the `AspNetUsers` table from our current database, right-click on it & select `Show Table Data`, next click on the Script button, finally copy the sql queries into our migration script.
* Again do the same procedure for tables `AspNetRoles` & `AspNetUserRoles`
* Finally remove the records from the databases from which we just extracted the SQL queries, because the migration will create them for us automatically.


## Working with Roles

* Now we need to restrict the admin actions to only the users which have the admin role, on the front-end side we can do this by:
  1. Displaying/hiding certain elements in the page
  2. We could just create a new view with only the necessary field in that

* Now on the backend action, we can add a condition to conditionally load on or the other view based on the role with the following code

```c#
    // `User` is a special property of our contorller which gets the information of the current user
    if(User.IsInRole("CanManageBooks")) {
        return View("AdminView");
    }
    else {
        return View("NormalUserView");
    }
```

* Now we need to restrict calls to our actions as well based on the role of the user, which can be done by adding
```c#
[Authorize(Roles = "CanManageBooks",Role2,Role3,...)]
```

## OAuth (Open Authorization)

* Organizations like Google, Facebook, etc. use the Oauth authentication protocol.
* For using, for ex. google, oauth we would need to register our website to the facebook oauth provider.
* After registring, google will give us an `API key` & `a secret`.
* Whenever a user wants to register using oAuth, we will redirect them to google & also pass the `key & secret`, so that google knows that the request has been arrived from our website, & not from some other website
* For keeping this API key & secret a secret, we need to use the `HTTPS protocol`.
* After the user logs in into their google account, google will tell them that our website is requesting access to their details.
* After the user authorizes google to send its basic information to our website, google will send us an `Authorzation Token`.
* On receiving the authorization token, as a safety measure, we will send the token, API key & secret back to google, just to verify that this token came from google & we are not being MITM.
* Google will finally receive this authorization token & after confirming, it will send us the `Access Token`.
* Using this access token, we can query to google to access some information related to the user.

### Enabling SSL

* Because we need to send these API keys & secrets through the internet, we will need to enable HTTPS, to ensure that no one can intercept this connection
* For enabling SSL, goto `Solution Explorer -> press F4 on our application -> Set SSL Enabled to True`. Copy the https url generated on that page.
* Now `Solution Explorer -> Right Click on our Application -> Properties -> Web (On the left menu) -> Servers -> Project Url -> Paste the URL here` & save
* Now the website will be accessible on https, but we can also access the application on http
* To disable http access to our website, add `filters.Add(new RequireHttpsAttribute())` to **FilterConfig.cs** file.

### Registering our application

* For registring our website on facebook, open [Facebook Developers](https://developers.facebook.com) & register an account
* Then click on our profile, then select `Add a New App`, then select the platform as `Website`
* Now enter the name of our application & click `Create new facebook ID`, on the next page, enter your web application's url, which can be https://localhost:12345
* On the next page, we will get the App ID & Secret, copy that into the `App_Start -> Startup.Auth.cs` as follows

```c#
public void ConfigureAuth(IAppBuilder app)
{
    ...
    // Uncomment the following lines to enable logging in with third party login providers

    app.UseFacebookAuthentication(
       appId: "",         // Enter the App ID here
       appSecret: "");    // Enter the secret here
    ...
}
```

# Optimization

* Always **AVOID** lazy loading of data from the relating Databases.
* When using lazy loading, data from a certain table is fetched only when it is needed, so if we are dynamically fetching data from a DB, we would have to make multiple calls to the DB which is slow, so we should always use `Eager Loading`, using the `Include` method

## Output Cache

### Views

* Instead of rendering the page for each request, we can create the web page only once & then we can cache it on the Client or Server location.
* So when the user request this page for the first time, we will create the page, but for subsequent requests, we can load the page from the cache
* Pages can be cached by using the `OutputCache` attribute on our action.

```c#
[OutputCache(Duration = 10, Location = OutputCacheLocation.Server, VaryByParam = "id")]
[AllowAnonymous]
[Route("Book/Books")]
public ActionResult Books()
{
    var books = _context.Books.Include(b => b.BookGenre).ToList();
    return View(books);
}
```

* In the OutputCache attribute we can also pass arguments to modify the behaviour of caching:
  1. `Duration`: the time in seconds after which this cache will be invalidate & the page will be rendered again
  2. `Location`: specifies where the page will get cached. It can be client (if the response is specific to a user) or it can be server (if the page is sam for all the users)
  3. `VaryByParam`: if our actions takes parameters & render the pages accordingly, we can cache the pages according to the parameter. For. ex if an action takes a bool parameter, then the page will cache the output for true & false case separately, after this method has been called once for each parameter
  4. `NoStore`: takes a boolean, if we dont want the page to cache at all, we can set this parameter to `true` & also set the Duration to be 0 // <-- XXX: Check this

* On VaryByParam parameter, we can also specify "\*", which will cache the request for each & every parameter
* Note: premature caching can show irrelevant/old data to the user, so only use this when a page is very slow & doesnt change frequently

### Data

* We can also cache the data from the DB & store it in our `MemoryCache` (RAM possibly // <-- XXX: Check this), we can store it by

```c#
using System.Runtime.Caching;
...

public ActionResult Index()
{
    // If the data is not in the memory already, load it into the MemoryCache
    if(MemoryCache.Default["Genres"] == null)
    {
        MemoryCache.Default["Genres"] = _context.Genres.ToList();
    }

    ...
}

public ActionResult GetGenre()
{
    // Now we can just use this cache in other methods
    var genres = MemoryCache["Genres"].ToList();
}

```

* Note: while using this approach can improve the performance of our application, it can increase the complexity of our code & also will increase the memory consumption of our application

## Async Await

* Using async-await does not increase the performance of our application, it just puts our thread on sleep while it is waiting for some event to complete like I/O, Network access
* This approach will just increase the scalability of our application & not the performance

## Disabling Session Storage

* In web applications, we have a special variable known as `Session` which can be used to save data related to user, which means more memory usage
* But when we are not using this, we can just disable this at all, which can be done by adding the following line in the `web.config` file

```xml
    <system.web>
        <sessionState mode="Off"></sessionState>
    </system.web>
```

# Deploying

## Publishing the application

* We will publish our package into a folder which can be selected in the Publish menu

## Deploying the database

* In the Package manager console, we can run the following command to get the SQL script

```powershell
Update-Database -script -SourceMigration:MigrationName5
```

* The `-script` option will tell the Entity framework to generate an SQL script which can be run to create the database
* Using `-SourceMigration` option we can specify from which migration the script should be generated instead of generating from our initial migration

## Application Settings

* Sometimes we want to store some sensitive information, for that we can store it in the web.config file in the <appSettings> section

```xml
    <appSettings>
        <add key="MySecretValue1" value="1234" />
        <add key="MySecretValue2" value="abcd" />
        ...
    </appSettings>
```

* Now in the code we can access these values by

```c#
using System.Configuration;
...

ConfigurationManager.AppSettings["MySecretValue1"]
ConfigurationManager.AppSettings["MySecretValue2"]

```

## Custome Error Pages

* For displaying custom error pages, we need to add the following line in the *web.config* file

```xml
<system.web>
    <!-- When set to on, it will show our custom error page to everyone -->
    <customErrors mode="On"></customErrors>
    <!-- When set to RemoteOnly, it will show our custom error page only to users which are not localhost -->
    <customErrors mode="RemoteOnly"></customErrors>
</system.web>
```

* To customize this error page, we need to modify the `Views/Shared/Error.cshtml` file
* This page is served by GlobalFilter from the `FilterConfig.cs` file. In that file we can see `filters.Add(new HandleErrorAttribute())`
* This is executed only when the application throws an exception, but will not be executed on an `404` error, it will just show the IIS error page

* To display custom error page for the 404 error, we need to modify the web.config file

```xml
<!-- The following only works when our application returns an HttpNotFound view -->
<customErrors>
    <error statusCode="404" redirect="~/Path/to/my/page.html">
</customErrors>

<!-- Modifies the IIS error page path directly -->
<system.webServer>
    <!-- Custom mode means that our custom error page will always be displayed -->
    <!-- DetailedLocalOnly means that custom error will be shown only when the connection is not localhost -->
    <httpErrors errorMode="Custom">
        <!-- Removing the IIS error page -->
        <remove statusCode="404">
        <error statusCode="404" path="myPath" responseMode="File">
    </httpErrors>
</system.webServer>

```

# Plugins

## Typeahead

* Used for input box text autocompletion
* Developed by twitter team
* `Install-Package Twitter.TypeAhead`

## toastr

* Displays toast notifications
* `Install-Package toastr`
* Example

```js
toastr.success("Success message");
toastr.error("Error message");
```

## Creating Roles using Code

* Following https://www.c-sharpcorner.com/UploadFile/asmabegam/Asp-Net-mvc-5-security-and-creating-user-role/
* There is another method of creating roles with only using code & not touching the database at all.
* For that, we can call a method at the web application start up to check if the role exists, if it doesnt then create.
* This approach will have a little overhead, but only at the start up of the application

* We can add the following function in the `startup.cs` file & call it from the `Configuration()`

```c#

private void CreateRoles()
{
    var context = new ApplicationDbContext();

    var roleStore = new RoleStore<IdentityRole>(context);
    var roleManager = new RoleManager<IdentityRole>(roleStore);

    var userStore = new UserStore<ApplicationUser>(context);
    var userManager = new UserManager<ApplicationUser>(userStore);

    if(!roleManager.RoleExists("Admin"))
    {
        // Create Admin role if it doesnt exist already
        await roleManager.CreateAsync(new IdentityRole("Admin"));

        // Creating a default admin user
        var adminUser = new ApplicationUser
        {
            UserName = "Admin",
            Email = "admin@admin.com",
        };

        string password = "Passw0rd!";

        // Create the admin user
        var result = userManager.Create(adminUser, password);

        // If the user creation was successful, add the user to the admin role
        if(result.Succeeded)
        {
            userManager.AddToRole(adminUser.Id, "Admin");
        }

    }

    if (!roleManager.RoleExists("User"))
    {
        // Create User role if it doesnt exist already
        await roleManager.CreateAsync(new IdentityRole("User"));
    }
}
```

* All the above function does is that it will just check if roles exists or not in the DB, if it doesnt, it will create new Roles

* Now we can use these roles for authorization purposes

# Random Points

* Method in Controller are called as actions
* When returning a View from an Action, then the function returning it should have an equivalent view page associated with it, i.e. If we have an Action as follows
```c#
[Route("Customers/Details/{id}")]
public ActionResult Details(int id)
{
  ...
}
```
then it should also have a view as the name Details.cshtml. i.e. method Details should have Details.cshtml
* If we want to pass a List<Model> to view, we can directly do it with the View(), but in the view we need to specify the model as @model List<Model>
* ASP.NET core = ASP.NET MVC + ASP.NET Web API

* To roll-back to a migration, we can run `Update-Database -TargetMigration:"name_of_migration"`

