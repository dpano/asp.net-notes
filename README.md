# NOTES #

### Declare files in application ###
in App_Start\BundleConfig.cs you can set the scripts and css files

### Package manager console ###

Open package manager console: Tools -> NuGet package manager -> package manager console
- Enable migrations:  `PM> Enable-Migrations -EnableAutomaticMigrations`
- Add migrations: `PM> add-migration "whatever message"`
- Update database: `PM> update-database`
- Load specific migration: `PM> update-database -target {{name of the migration class}}`


### Crete new file ###
select a folder and click shift+F2

### String interpolation in C# ###

The following example is in a model property

```cs
[Display(Name = "Full Name")]
// Map FirstName field combined with LastName in FriendlyName
public string FriendlyName => $"{FirstName} {LastName}";
```

Warning! Before applying migrations make sure that you have refferenced Models in AplicationDbContext in Models -> IdentityModels.cs

### Authorization ###

You can use [Authorize] above controller or method to ensure that in order to have **access** to the specific **route** you got first to be **authenticated** through **login**

# Tutorial #

### Routing ###
file: Global.asax
registering routes in RegisterRoutes method: App_Start\RouteConfig.cs

**action** is just a method on the controller
```cs
public static void RegisterRoutes(RouteCollection routes){
         routes.IgnoreRoute("{resource}.axd/{*pathInfo}");
         routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new{ controller = "Home", action = "Index", id = UrlParameter.Optional});
      }
```

#### Custom convention ###
Here we can change the default action to be "List"
```cs
namespace MVCFirstApp{
   public class RouteConfig{
      public static void RegisterRoutes(RouteCollection routes){
         routes.IgnoreRoute("{resource}.axd/{*pathInfo}");

         routes.MapRoute(
            "Process", "Process/{action}/{id}",
            defaults: new{
               controller = " Process", action = "List ", id =
               UrlParameter.Optional});

         routes.MapRoute(
            name: "Default", url: "{controller}/{action}/{id}",
            defaults: new{
               controller = "Home", action = "Index", id =
               UrlParameter.Optional});
      }
   }
}
```
### Controllers ###
Get the last param of the url **NOT** as an action but as a parameter to pass into the controller method
```cs
routes.MapRoute(
            "Employee", "Employee/{name}", new{
               controller = "Employee", action = "Search", name = UrlParameter.Optional });
.
.
.
// action inside the EmployeeController
// GET: Employee
      public ActionResult Search(string name){
         var input = Server.HtmlEncode(name);
         return Content(input);
      }		
```
So when /Employee/Mark url is called the UI is going to show:
`Mark`

### Actions ###
Return html to view through controller method
```cs
public string GetAllCustomers(){
   return @"<ul>
      <li>Ali Raza</li>
      <li>Mark Upston</li>
      <li>Allan Bommer</li>
      <li>Greg Jerry</li>
   </ul>";
}
```
Redirect to actions for the same controller or even for a different controller.

```cs
public ActionResult Index(){
         return RedirectToAction("GetAllCustomers","Customer");
      }
```

### Action filters ###
**Filters** are custom classes that provide both a declarative and programmatic means to **add pre-action** and **post-action** behavior to controller action methods.

> Action filters can be applied to a controller action or an entire controller

- **OutputCache** − Caches the output of a controller action for a specified amount of time.
- **HandleError** − Handles errors raised when a controller action is executed.
- **Authorize** − Enables you to restrict access to a particular user or role.

##### Types of filters #####

- **Authorization** Filters − Implements the `IAuthorizationFilter` attribute.
- **Action Filters** − Implements the `IActionFilter` attribute.
- **Result Filters** − Implements the `IResultFilter` attribute.
- **Exception Filters** − Implements the `IExceptionFilter` attribute.
 > Filters are executed in the order listed above

##### Apply action filter #####

This filter causes the value returned by the action to be cached for 15 seconds.

```cs
public class HomeController : Controller{
      // GET: Home
      [OutputCache(Duration = 15)]

      public string Index(){
         return "This is ASP.Net MVC Filters Tutorial";
      }
   }
```

##### Custom filters #####

To create your own filter ASP.NET MVC provides a base class `ActionFilterAttribute`

A use case can be to create a logger for Controllers and/or actions

### Selectors ###

Action selectors are attributes that can be applied to action methods and are used to influence which action method gets invoked in response to a request. It helps the routing engine to select the correct action method to handle a particular request.

Types of selections:
* ActionName
* NonAction
* ActionVerbs

##### ActionName #####

This class represents an attribute that is used for the name of an action

```cs
[ActionName("CurrentTime")]
      public string GetCurrentTime(){
         return DateTime.Now.ToString("T");
      }
```
To access that method the url should be changed to http://localhost:62833/Home/CurrentTime instead of http://localhost:62833/Home/GetCurrentTime


##### NonAction #####

Indicates that a public **method** of a Controller is **not** an **action** method

##### ActionVerbs #####

This restricts the indication of a specific action to specific HttpVerbs. You can define two different action methods with the same name but one action method responds to an HTTP Get request and another action method responds to an HTTP Post request.

List of action verbs:

* HttpGet
* HttpPost
* HttpPut
* HttpDelete
* HttpOptions
* HttpPatch


```cs
public ActionResult Search(string name){
         var input = Server.HtmlEncode(name);
         return Content(input);
      }

      [HttpGet]
      public ActionResult Search(){
         var input = "Another Search action";
         return Content(input);
      }
```

### Views ###

A controller action can return a view, a string or it might perform some other type of actions such as redirecting etc.

access http://localhost/Home/Mycontroller and the output will be that string
```cs
public string Mycontroller(){
         return "Hi, I am a controller";
      }
```

to return a View
```cs
public ActionResult MyView(){
         return View();
      }
```
but the code above is going to return a server error in the case where the view file does not exist

> to create a view file, right click in Myview method and click add view

### Data model ###
A model stores data that is retrieved according to the commands from the Controller and displayed in the View. Model is a collection of classes wherein you will be working with data and business logic.

### Helpers ###

Types of helper methods:

- **Createinputs** − Creates inputs for text boxes and buttons.
- **Createlinks** − Creates links that are based on information from the routing tables.
- **Createforms** − Create form tags that can post back to our action, or to post back to an action on a different controller.

```cs
@Html.ActionLink("Edit", "Edit", new { id = item.ID })
```
> **HTML** is a property that we inherit from the **ViewPage** base class. It's available in all of our views and it returns an instance of a type called HTML Helper

**HTML.BeginForm** writes an opening Form Tag

**HTML.HiddenFor**, which emits the hidden field.

**Html.LabelFor** HTML Helper creates the labels on the screen


### Model binding ###

ASP.NET MVC model binding allows you to map HTTP request data with a model. It is a well-designed bridge between the HTTP request and the C# action methods. It makes it easy for developers to work with data on forms (views), because POST and GET is automatically transferred into a data model you specify. ASP.NET MVC uses default binders to complete this behind the scene.

When the user enters values on **Create View** then it is available in **FormCollection** as well as **Request.Form**. We can use any of these values to populate the employee info from the view.

```cs
// POST: Employee/Create
[HttpPost]
public ActionResult Create(FormCollection collection){
   try {
      Employee emp = new Employee();
      emp.Name = collection["Name"];
      DateTime jDate;
      DateTime.TryParse(collection["DOB"], out jDate);
      emp.JoiningDate = jDate;
      string age = collection["Age"];
      emp.Age = Int32.Parse(age);
      empList.Add(emp);
      return RedirectToAction("Index");
   }catch {
      return View();
   }
}
```


### Databases ###

The **Entity Framework** (EF) supports Code First technique, which allows you to create model objects by writing simple classes and then the database will be created on the fly from your classes, which enables a very clean and rapid development workflow.

##### Add DBContext #####

```cs
public class EmpDBContext : DbContext{
      public EmpDBContext()
      { }
      public DbSet<Employee> Employees { get; set; }
   }
```
As seen above, EmpDBContext is derived from an EF class known as DbContext. In this class, we have one property with the name DbSet, which basically represents the entity you want to query and save.

##### Connection string #####

We need to specify the connection string under <configuration> tag for our database in the Web.config file.

```xml
<connectionStrings>
   <add name = "EmpDBContext" connectionString = "Data
   Source = (LocalDb)\v14.0;AttachDbFilename = |DataDirectory|\EmpDB.mdf;Initial
   Catalog = EmployeeDB;Integrated Security = SSPI;"
   providerName = "System.Data.SqlClient"/>
</connectionStrings>
```
First we add create a private EmpDBContext class object and then update the Index, Create and Edit action methods

```cs
private EmpDBContext db = new EmpDBContext();
      // GET: Employee

      public ActionResult Index(){
         var employees = from e in db.Employees
         orderby e.ID
         select e;
         return View(employees);
      }

      // GET: Employee/Create
      public ActionResult Create(){
         return View();
      }

      // POST: Employee/Create
      [HttpPost]
      public ActionResult Create(Employee emp){
         try{
            db.Employees.Add(emp);
            db.SaveChanges();
            return RedirectToAction("Index");
         }catch{
            return View();
         }
      }

      // GET: Employee/Edit/5
      public ActionResult Edit(int id){
         var employee = db.Employees.Single(m => m.ID == id);
         return View(employee);
      }

      // POST: Employee/Edit/5
      [HttpPost]
      public ActionResult Edit(int id, FormCollection collection){
         try{
            var employee = db.Employees.Single(m => m.ID == id);
            if (TryUpdateModel(employee)){
               //To Do:- database code
               db.SaveChanges();
               return RedirectToAction("Index");
            }
            return View(employee);
         }catch{
            return View();
         }
      }
```

### Validation ###

##### DRY #####

DRY stands for **Don't Repeat Yourself** and is one of the core design principles of ASP.NET MVC

##### Adding Validation to Model #####

Validation attributes: **Required**, **StringLength**, **RegularExpression**, and **Range**

```cs
[StringLength(60, MinimumLength = 3)]
public string Name { get; set; }

[Display(Name = "Joining Date")]
[DataType(DataType.Date)]
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime JoiningDate { get; set; }

[Range(22, 60)]
public int Age { get; set; }
```

##### Apply migrations ###

Open **Package Manageer Console** in Visual Studio and execute the commands one by Content
```
PM> Enable-Migrations
PM> add-migration DataAnnotations
PM> update-database
```

### Security ###

##### Authentication #####

Authentication of user means verifying the identity of the user

> The default authentication is, **Individual User Accounts**.

##### Authentication Options #####

- **No Authentication**
- **Individual User Accounts**: is the traditional forms-based authentication where users can visit a website. They can register, create a login, and by default their username is stored in a SQL Server database using some new ASP.NET identity features
- **Work and School Accounts**: organizational accounts, business applications using active directory federation services
- **Windows Authentication**: intranet applications

Display username, in loginpartial.cshtml

```html
<ul class = "nav navbar-nav navbar-right">
    <li>
       @Html.ActionLink("Hello " + User.Identity.GetUserName() + "!",
       "Index", "Manage", routeValues: null, htmlAttributes: new { title = "Manage" })
    </li>

    <li>
       <a href = "javascript:document.getElementById('logoutForm').submit()">Logoff</a>
    </li>

 </ul>
```

Control the view and show content for authenticated users
```
@if (Request.IsAuthenticated) {
```

##### Authorization #####

Protect action and keep unauthenticated users from arriving here

```cs
// GET: Secret
[Authorize]
public ContentResult Secret(){
   return Content("Secret informations here");
}
```

`[Authorize]` can be used in Controller or action level

If **[Authorize]** is applied to contoller level, you can remove it from actions by adding **[AllowAnonymous]**

```cs
namespace MVCSecurityDemo.Controllers{
   [Authorize]
   public class SecretController : Controller{
      // GET: Secret
      public ContentResult Secret(){
         return Content("Secret informations here");
      }

      [AllowAnonymous]
      public ContentResult PublicInfo(){
         return Content("Public informations here");
      }
   }
}
```

With the Authorize attribute, you can also specify some parameters.
```cs
namespace MVCSecurityDemo.Controllers{
   [Authorize(Users = "ali.khan@outlook.com")]
   public class SecretController : Controller{
      // GET: Secret
      public ContentResult Secret(){
         return Content("Secret informations here");
      }

      [AllowAnonymous]
      public ContentResult PublicInfo(){
         return Content("Public informations here");
      }
   }
}
```

### Caching ###

Caches the resource for 60 seconds

```cs
[OutputCache(Duration = 60)]
public ActionResult Index(){
   var employees = from e in db.Employees
   orderby e.ID
   select e;
   return View(employees);
}
```

##### Varying the Output Cache #####

In some cases, you might want different cached versions, such as, when you create a detail page, then when you click on the detailed link you will get details for the selected employee

Take advantage of the **VaryByParam** property of the **[OutputCache]** attribute
```cs
// GET: Employee/Details/5
[OutputCache(Duration = int.MaxValue, VaryByParam = "id")]

public ActionResult Details(int id){
   var employee = db.Employees.SingleOrDefault(e => e.ID == id);
   return View(employee);
}
```
##### Cache Profile #####

Create a cache profile in the `web.config` file

Advantages:

- Controls how controller actions cache content in one central location.
- Creates one cache profile and apply the profile to several controllers or controller actions.
- Modifies the web configuration file without recompiling your application.
- Disables caching for an application that has already been deployed to production.

```xml
<caching>
   <outputCacheSettings>
      <outputCacheProfiles>
         <add name = "Cache10Min" duration = "600" varyByParam = "none"/>
      </outputCacheProfiles>
   </outputCacheSettings>
</caching>
```

```cs
[OutputCache(CacheProfile = "Cache10Min")]
```

### Razor ###

FOR loop

```html
@{
   Layout = null;
}

<!DOCTYPE html>
<html>
   <head>
      <meta name = "viewport" content = "width = device-width" />
      <title>Index</title>
   </head>

   <body>
      <div>
         @for (int index = 0; index < 12; index++){
            <div>@index </div>
         }
      </div>
   </body>

</html>
```

### Data Annotations ###

DataAnnotations is used to configure your model classes

**System.ComponentModel.DataAnnotations** includes the following attributes that impacts the nullability or size of the column.

- Key
- Timestamp
- ConcurrencyCheck
- Required
- MinLength
- MaxLength
- StringLength


**System.ComponentModel.DataAnnotations.Schema** namespace includes the following attributes that impacts the schema of the database.

- Table
- Column
- Index
- ForeignKey
- NotMapped
- InverseProperty


##### Key #####

Entity Framework relies on every entity having a key value that it uses for tracking entities.

```cs
public class Student{
   [Key]
   public int StdntID { get; set; }
   public string LastName { get; set; }
   public string FirstMidName { get; set; }
   public DateTime EnrollmentDate { get; set; }

   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```
**Composite keys** primary keys that consist of more than one property. In the example below, the primary key is a combination of LicenseNumber and IssuingCountry.

```cs
public class DrivingLicense{
   [Key, Column(Order = 1)]
   public int LicenseNumber { get; set; }

   [Key, Column(Order = 2)]
   public string IssuingCountry { get; set; }
   public DateTime Issued { get; set; }
   public DateTime Expires { get; set; }
}
```

>Entity Framework requires you to define an **order** of the key properties

##### Timestamp #####

Code First will treat Timestamp properties the same as ConcurrencyCheck properties, but it will also ensure that the database field generated by Code First is non-nullable.

```cs
public class Course{
   public int CourseID { get; set; }
   public string Title { get; set; }
   public int Credits { get; set; }
   [Timestamp]
   public byte[] TStamp { get; set; }

   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```

##### ConcurrencyCheck #####

The ConcurrencyCheck annotation allows you to flag one or more properties to be used for concurrency checking in the database, when a user edits or deletes an entity.

```cs
public class Course{
   public int CourseID { get; set; }

   [ConcurrencyCheck]
   public string Title { get; set; }
   public int Credits { get; set; }

   [Timestamp, DataType("timestamp")]
   public byte[] TimeStamp { get; set; }

   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```
Code First will include Title column in update command

##### Required #####

The Required annotation tells EF that a particular property is required.

```cs
public class Student{
   [Key]
   public int StdntID { get; set; }

   [Required]
   public string LastName { get; set; }

   [Required]
   public string FirstMidName { get; set; }
   public DateTime EnrollmentDate { get; set; }

   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```

##### MaxLength #####

It can be applied to a string or array type property of a domain class. EF Code First will set the size of a column as specified in MaxLength attribute.

```cs
public class Course{
   public int CourseID { get; set; }
   [ConcurrencyCheck]
   [MaxLength(24)]

   public string Title { get; set; }
   public int Credits { get; set; }

   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```

##### MinLength #####

Can be used like MaxLength and in combination

```cs
public class Course{
   public int CourseID { get; set; }
   [ConcurrencyCheck]
   [MaxLength(24) , MinLength(5)]
   public string Title { get; set; }
   public int Credits { get; set; }

   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```

##### StringLength #####

```cs
public class Course{
   public int CourseID { get; set; }
   [StringLength (24)]
   public string Title { get; set; }
   public int Credits { get; set; }

   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```


##### Table #####

Default Code First convention creates a table name same as the class name.
To change the name of the table:

```cs
[Table("StudentsInfo")]
public class Student{
   [Key]
   public int StdntID { get; set; }

   [Required]
   public string LastName { get; set; }

   [Required]
   public string FirstMidName { get; set; }
   public DateTime EnrollmentDate { get; set; }

   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```

You cannot only specify the table name but you can also specify a schema for the table

```cs
[Table("StudentsInfo", Schema = "Admin")]
```

##### Column #####

Overrides the column name

```cs
public class Student{
   public int ID { get; set; }
   public string LastName { get; set; }

   [Column("FirstName")]
   public string FirstMidName { get; set; }
   public DateTime EnrollmentDate { get; set; }

   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```

##### Index #####

```cs
[Index]
public int Credits { get; set; }
```

> By default, indexes are non-unique, but you can use the **IsUnique**

```cs
[Index(IsUnique = true)]
public string Title { get; set; }
```

##### ForeignKey #####

Code First convention will take care of the most common relationships in your model, but there are some cases where it needs help. For example, by changing the name of the key property in the Student class created a problem with its relationship to Enrollment class.

```cs
public class Enrollment{
   public int EnrollmentID { get; set; }
   public int CourseID { get; set; }
   public int StudentID { get; set; }
   public Grade? Grade { get; set; }
   public virtual Course Course { get; set; }
   public virtual Student Student { get; set; }
}

public class Student{
   [Key]
   public int StdntID { get; set; }
   public string LastName { get; set; }
   public string FirstMidName { get; set; }
   public DateTime EnrollmentDate { get; set; }

   public virtual ICollection<Enrollment> Enrollments { get; set; }
}
```

The solution for this is to create a navigation property in the Enrollment and use the ForeignKey DataAnnotation to help Code First understand how to build the relationship between the two classes as shown in the following code.

```cs
public class Enrollment{
   public int EnrollmentID { get; set; }
   public int CourseID { get; set; }
   public int StudentID { get; set; }
   public Grade? Grade { get; set; }
   public virtual Course Course { get; set; }

   [ForeignKey("StudentID")]
   public virtual Student Student { get; set; }
}
```

##### NotMapped #####

When you do not want to create a column in a database

```cs
[NotMapped]
   public int FatherName { get; set; }
```

##### InverseProperty #####

The InverseProperty is used when you have multiple relationships between classes.

# LINKS #

- [Repository pattern](https://deviq.com/design-patterns/repository-pattern)
- [ASP.NET MVC tutorial](https://www.tutorialspoint.com/asp.net_mvc/index.htm)
- [Sofware design patterns](https://sourcemaking.com/)
