---
layout: post
title: "Annotation-based Role access security for Spring Boot - roles configuration on MongoDB"
comments: true
categories: Java
---

Working on a research project in my company, I needed a nice way to define Security Access to my services. Java Annotation is the answer. Read the story to see the code details <!--more-->

The system is based on a microservices architecture using spring boot as actuator. Users are registered on MongoDB in User collection with the following schema example:

{% highlight javascript %}
> db.User.find().pretty()
{
	"_id" : ObjectId("56351188da060d8a43bfe108"),
	"username" : "dino.lupo",
	"hashedPassword" : "$2a$10$mRDKUyd01khAgBoOFmFei.tcY59YjzO8FXSjz4fonnLUlt7TAl8R.",
	"email" : "dino.lupo@gmail.com",
	"firstname" : "Dino",
	"lastname" : "Lupo",
	"roles" : [
		"write-user",
		"read-user",
		"admin"
	]
}
{% endhighlight %}

I needed a way to annotate my spring Controller methods with a ```@Role``` annotation as follows:

{% highlight java %}
@Controller
@EnableAutoConfiguration
public class UserController {
// ...
	/**
	 * Return the list of all Users that are registred
	 * @return
	 */
	@Role(access="read-user")
	@RequestMapping(value = "/protected/allUsers", method = RequestMethod.GET, produces = MediaType.APPLICATION_JSON_VALUE)
	@ResponseBody
	public ResponseEntity<Object> getAllUsers(HttpServletRequest request, HttpServletResponse response) {
	// ...
	}
// ...
}
{% endhighlight %}

In the previous example, I wanted to permit the access to the web method only to connected users with a "read-user" Role configured in my MongoDB.

The code is at the following GitHub repo [GitHub Repository](https://github.com/dinolupo/role-annotation-sprinboot-mongodb)

It is composed by two annotations definition and some other configuration classes:

> @EnableRoleChecking

{% highlight java %}
@Inherited
@Import(RoleConfiguration.class)
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
public @interface EnableRoleChecking {
}
{% endhighlight %}

This class is needed by the root Spring Boot Application to configure the Interception Handlers. 

> @Role 

{% highlight java %}
@Inherited
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD})
public @interface Role {
	String access() default "guest";
}
{% endhighlight %}

The ```@Role``` Annotation is needed if you want to restrict access to a method only by connected user that has the right role in the database.

The ```RoleConfiguration``` class declares  the SecurityInterceptor Bean that is needed to check the roles into the DB and the ```RoleConfigurationProperties``` declares the field needed by the SecurityHandler to work properly.

* You have to Add the following required properties

> in Spring Boot's `application.properties` or `application.yml` Example:

{% highlight yaml %}
spring:
  data:
    mongodb:
      database: UserRegistrationDB
      host: localhost
      port: 27017

temotec:
  annotations:
    security:
      collection: User
      role-path: roles
      username-path: username
{% endhighlight %}
 
"collection" is the name of the MongoDB collection where the Users are stored.
"role-path" is the name of the property where roles are stored for the User.
"username-path" is the name of the property that corresponds to the username of the connected user.
 
The most important class is the SecurityInterceptor that extends the HandlerInterceptorAdapter, there are a lot of resource on the web on how this works, but essentially it declares 3 methods PreHandle, PostHandle and AfterCompletion that you can you to execute code before, after and at the end of the intercepted method. In the SecurityInterceptor I override only the PreHandle to verify the role:


{% highlight java %}
@Component
public class SecurityInterceptor extends HandlerInterceptorAdapter {
	
	@Autowired
	MongoClient mongoClient;
	
	@Autowired
	RoleConfigurationProperties roleProperties;
	
	@Autowired
	MongoProperties mongoProperties;
	
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		
		String connectedUser = request.getUserPrincipal() == null ? null : request.getUserPrincipal().getName();
		
		// if no user is logged in, then exit
		if (connectedUser == null)
			return false;
		
		// cast the annotated spring handler method
		HandlerMethod handlerMethod = (HandlerMethod) handler;
		// get method using reflection
		Method method = handlerMethod.getMethod();
		// check if the method is annotated
		if (handlerMethod.getMethod().isAnnotationPresent(Role.class)) {
			Annotation annotation = method.getAnnotation(Role.class);
			Role casRole = (Role) annotation;
			
			System.out.printf("Access :%s\n", casRole.access());
			
			MongoDatabase db = mongoClient.getDatabase(mongoProperties.getDatabase());
			MongoCollection<Document> coll = db.getCollection(roleProperties.getCollection(), Document.class);
			Bson filter = and( eq(roleProperties.getUsernamePath(), connectedUser), 
			                   eq(roleProperties.getRolePath(), 	casRole.access())
							 );
			
			long found = coll.count(filter);
			
			System.out.printf("Found: %d\n", found);

			if (0 == found) {
				response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
				return false;
			}
		}
		
		return true;
	}
}
{% endhighlight %}

The interceptor is quite straightforward, it uses the mongodb driver to find if the connected user has the annotated role into his own record.

To see how to I used it, go to the GitHub repository and read the instructions [Readme](https://github.com/dinolupo/role-annotation-sprinboot-mongodb)
  
