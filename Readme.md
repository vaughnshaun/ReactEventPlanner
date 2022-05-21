1. Create Project from .NET 5.0 React Template
2. npm install, npm audit (Lots of security risk maybe move to react scripts 5 and create react app 2)
3. Replace spa.UseReactDevelopmentServer(npmScript: "start"); with
	spa.Options.SourcePath = "ClientApp";
      spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
	// The default way gets stuck in a process.
4. Run project to make sure it works
	a. npm start
	b. Run server from Visual Studio
	c. Click around
5. Grab a .gitignore for the solution and place it in the folder the solution resides in
6. Push new project to git
	a. git init // create local repository
	b. git status // Check correct files will be added
	c. git add . // Add files and folders to the index
	d. git status // Check that all expected files are in stagin (index)
	e. git commit -m "my message" // Commit to the local repository
	f. Go to GitHub and add a new remote repository
	g. git remote add origin https://github.com/vaughnshaun/ReactEventPlanner.git // Add a remote repository to the list of remotes
	h. git checkout -b main // master no longer exists in Github it is now main
	i. git branch -d master // delete master
	j. git push -u -f origin main // Push code to the main branch in the remote. -u: make remote default -f: force overwrite everything in the remote repository
	The local branch is now auto tracked
7. Migrate .NET 5.0 to .NET 6.0
	a. Switch target framework
	b. Install Microsoft.AspNetCore.SpaProxy.spaproxy // I might not need this
	c. Move Startup.cs logic to Program.cs (Optional)
	d. Test App
8. Right click project -> add -> docker support
9. Install Asp.NET Core Identity. Installing the Identity API
	Important concrete: UserManager, SigninManager. UserManager handles CRUD for users. 
	SigInManager handles signing in and logging off.
	
	a. Install-Package Microsoft.AspNetCore.Identity // Contains all the models for the API
		i. IdentityRole
		ii. IdentityRoleClaim
		iii. IdentityUser
I		iv. dentityUserClaim
		v. IdentityUserLogin
		vi. IdentityUserRole
		vii. IdentityUserToken
	b. Install-Package Microsoft.EntityFrameworkCore.SqlServer // The database provider for SQL Server
	c. Install-Package Microsoft.EntityFrameworkCore.Tools // This is for database migrations
	d. Add connection string to appsettings.json

		```
		"ConnectionStrings": {
			"DefaultConnection": "Server=LocalHost;Database=ASPNetCoreIdentity;Trusted_Connection=True;MultipleActiveResultSets=true"
		}
		```

	e. Extend IdentityUser to make your own custom ApplicationUser
10. Get access to database
	a. Inherit from DBContext to gain APIs that access the database

		```
		public class ApplicationDbContext : IdentityDbContext<IdentityUser, IdentityRole, string>
		{
			public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
		: base(options)
			{
			}
		}
		```

	b. Register the database context

		```
		services.AddDbContext<ApplicationDbContext>(options =>
			options.UseSqlServer(
				Configuration.GetConnectionString("DefaultConnection")
		));
		```

	c. Create the Database

		```
		add-migration v1
		update-database
		```

	d. Register the Identity Services

		```
		services.AddIdentity<IdentityUser, IdentityRole>(
			options => {
				options.SignIn.RequireConfirmedAccount = false;

				// Add more options
			}
		)
		.AddEntityFrameworkStores<ApplicationDbContext>
		```

	e. Add Authentication Middleware (add after routing and before UseEndpoints and UseAuthorization)

		```
		app.UseAuthentication();
		app.UseAuthorization();
		```

	f. Configure the cookie options such as redirect url and expiration
9. Sql Server SHA2_256 salt and hash HASHBYTES('SHA2_256', lower(@EmailAddress) + @Password + @salt)

// More on Identity
https://www.tektutorialshub.com/asp-net-core/asp-net-core-identity-tutorial/