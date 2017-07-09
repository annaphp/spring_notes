# Spring review questions

## Core Spring

What is spring
- Spring is an application framework which main features are dependency injection and aspect oriented programming.

Why use spring over regular Java
 - because of a dependency injection that Spring offers
 
What is DI?
 - DI stands for a dependency injection which is a way to provide required references (dependencies) to the objects
   in your application.

3 ways to configure spring container, describe each in detail
 - Java Cofiguration - a Java configuration class is created annotated with @Configuration where beans are created and 
   dependancies are defined.(Use @Bean to declare a bean)
 - Autowiring and Component Scan - to enable autowiring and component scan, create a configuration java class that should
   be annotated with @Configuration and @ComponentScan (you can also create xml config file and enable componentScan in
   there). @Component annotation is used to label all classes for which a bean should be created by Spring. To provide a 
   reference to an object (DI) @Autowired is used.
 - XML configuration - xml file is used to configure spring container, create an xml file and declare beans in there using
   <bean> element. 

describe default bean naming in all 3 configuration types, and how to override these defaults
 - Java Configuration - default name is @Bean annotated method name,  to override default name  - @Bean(name="newName")
 - Autowiring and ComponentScan - default name is a class name in lowercase, to override default name - @Component("newName")
 - XML configuration - default name is a class name#number, to override default name use id attribute - <bean id="newName"   class="MyClass"/>

name all possible ways to mix configurations in spring
  - Beans declared in xml config and java config are available in Autowiring, thus Autowiring can be mixed with Java and xml 
    configuration
  - Java Configuration can be mixed with xml config or another java config. To import beans from another java config file 
    into a java config use @Import(AnotherJavaConfig.class), to import beans from xml file into a java config use
    @ImportResource("xmlConfig.xml")
  - XML config can be mixed with java config or another xml config file. To import beans from another xml config file into
    xml config use <import resource="another-config.xml"/>, to import java config into xml declare java config class as a 
    bean in xml config using <bean class="JavaConfig" /> element.

name 4 bean scopes, which ones are pertinent to SpringMVC?
 - Singleton - one per container
 - Prototype - one per reference (meaning there will be a new instance of the class created when the bean is injected)
 - Session - exists only during a web session
 - Request - exists only during a HTTP Request
which ones are pertinent to SpringMVC - session and request scopes

what is AOP?
Aspect Oriented Programming is a programming concept of separating cross-cutting concerns(logging, security) from the application business logic.

How do we use AOP in spring, give 3 examples
- transactions
- logging
- security

Describe domain language used in describing aspects
 - Aspect - a combination of advice and pointcuts encapsulated in a class.

 - Joinpoints are all possible points during the execution of a program where an advice can be applied. 

 - Pointcuts - collection of join points, where exactly advice should be run.

 - Advice - is a method that is run at particular joinpoint, in other words what code and when it should be run. There are 5 kinds of advice:
 * Before - advice is called before the advised method is called
 * After - advice is called after the advised method completes, regardless whether the method completion was successful or not.
 * After-returning - after the the advised method successfully completes.
 * After-throwing - after the advised method throws an exception.
 * Around - advice wraps the advised method, before and after the advised method is invoked.

Spring aspects are implemented as proxies that wrap the target object (the object that does business logic). 
The proxy handles method calls, performs additional aspect logic and then calls the target method.


How to start spring context in a main method? 

Javaconfig:

	ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);			
	Game game = context.getBean("cricketGame", Game.class);
	System.out.println(game.playGame());
		
XML:		

	ApplicationContext context = new ClassPathXmlApplicationContext("context_config.xml");
	Game game = context.getBean("game", Game.class);
	System.out.println(game.playGame());

## JDBC

Describe JDBC without spring
 - JDBC DriverManager class gives you connection with DB -> DriverManager.getConnection("jdbc:mysql://localhost:3306/test", "username", "password");
 - Connection allows you create statements -> Statement statement = connection.createStatement();
 - You can execute statements using statement object -> statement.execute("insert into cart values("+cart.getId()+
   +", '" + cart.getDescription() +"' )");
 - To read from DB -> ResultSet rs = stmt.executeQuery("select *  from cart where id =" + id +";");
   then you need save your object from resultSet -> Cart c = null;
						    if(rs.next()){
							c = new Cart(rs.getInt("id"), rs.getString("description"));	
						     }
 
Describe how to perform joins in SQL(name some common joins)
- Inner join - retrieves rows form table A and table B that have matching values in both tables. For example:
	SELECT books.name, authors.name FROM books, authors WHERE authors.id = books.author_id;
- Left Join - retrieves all rows form table A even if there are no matching rows in table B. For example:
	SELECT books.name, authors.name FROM books LEFT JOIN authors ON books.author_id = authors.id;
- Right Join - retrieves all rows from table B even if there are no matching rows in table A. For example:
	SELECT books.name, authors.name FROM books RIGH JOIN authors ON books.author_id = authors.id;
	

Describe transactions in SQL/JDBC
Transactions have the following properties:

- Atomicity - All operations within a transaction are completed, the transaction is rolled 
back if one of the operations fails.

- Consistency - data should be saved always according to the business rules

- Durability - ensures that the result of a committed transaction persists in case of a system failure.

- Isolation - what your transaction sees in relation to changes made by others.
Isolation enables transactions to operate independently of and transparent to each other.

Describe 3 concurrency phenomenas that can be observed in the database
 - dirty read - your transaction sees uncommitted changes
 - non-repeatable read - when you read the same row in your transaction twice and get different results
 - phantom read - when you calculate a value based on the number of rows in your transaction twice and get a different result
   because extra rows were added by another user

Describe transaction isolation levels
 - Read uncommitted - allows dirty read, non-repeatable reads and phantom reads
 - Read Committed - allows non-repeatable and phantom reads
 - Repeatable read - allows phantom reads only
 - Serializable - doesn't allow dirty reads, non-repetable reads and phantom reads

Describe how we can model many-to-one relationship in the DB
 - many side should have a foreign key column referring to id of one side

Describe how to model many-to-many relationship in the DB
 - there should be a third table created to store many-to-many relationship(table one ids and table two ids)

Describe Spring's approach to JDBC, name key interfaces/classes:
 - Spring simplifies JDBC use by providing JDBC template which hides DB access boiler plate code.
   With JdbcTemplate you only need to write code that is related to data manupulation (CRUD).

What is a repository?
 - Repository is a class where CRUD functions are performed, a class that represents persistence layer.
 
What is a datasource? How is it different from drivermanager?
- Datasource represents database connection. It's different from drivermanager because it has a connection pool and 
  it manages connections (connections are always open and when connection is not used it's put back into the connection pool
  and is available for other requests) whereas with drivermanager you need to open/close connections.
  
Describe steps to configure JDBC based repository in Spring
- To enable JdbcTemplate in your application the following steps have to be completed:
	- set up required dependencies
	- define Datasource bean in Spring dependency config file
	  DataSource represents a database, it has a connection pool which contains a set of 
	  connections. DataSouce requires jdbc driver, database url, database username and password, connection pool initial
	  and maximum size.
	- create Repository class for your model with JdbcTemplate field
	

## JPA

What is JPA?
- ORM(Object Relationship Mapping) Specification

What is Hibernate? compare Hibernate and JPA

- Hibernate - ORM framework (it has sessionFactory(you build sessionFactory = buildSessionFactory()),
  sessionFactory gives you session (sessionFactory.openSession()), on session you can call methods
  such as session.beginTransaction,  session.getTransaction.commit() and session.close()).
  
- In JPA, there is EntityManagerFactory that gives you an instance of EntityManager. EntityManager allows you
  to persist objects and manage transactions.

JPA is an interface (a standard for ORM), Hibernate is an implementation that satisfies JPA standards.

Name key classes/interfaces in JPA
- EntityManagerFactory (defined in config) and  EntityManger (used in Repository)

What are entities?
- User defined classes instances of which should be stored in db

Name entity states
- Transient (new not saved)
- Persistent, managed (saved, there is a row in db associated with this object)
- Detached (retrieved from db, and changes are not synchronized with db)
- Removed - to be deleted

What are value types? (Basic types like String and classes annotated with @Embeddable)
 - @Embeddable can be other classes, Int, Long.
 
How can an entity store its collection of values types, what about one value type?
- Using @ElementCollection you can save a collection of basic Java types such as String,Integer, Long)
  @ElementCollection is also used to store a collection of @Embedable 
  
Name 2 main types of relationships between entities?
- unidirectional and bidirectional

Name 4 main cardinalities
- One to many, many to one, many to many, one to one

Name 7 possible combinations of cardinalities and directions. Why there are only 7 and not 8?
- Unidir - one to one
- unidir - one to many
- unidir - many to one
- unidir - many to many

- bidir - one to one
- bidir - many to one (one side is OneToMany and the other is ManyToOne)
- bidir - many to many

What is unique about unidirectional relations, who owns the relation?
- In unidirectional relations, one side that owns the relationship has a reference to the 
  other side, object navigation is one way, the side that has the annotations owns the
  relationship. Owning side (one side) has to be updated after relationship is created.

What is unique about bidirectional relations? who owns the relations?
- typically many side owns the relationships, the side that has the annotation and doesn’t have
  mappedby=“fieldNamefromOwningSide” is the relationship owner.The owner side is updated.
  
How do we mark an owning side in a bidirectional relation? What is inverse side?
- With annotation (for example @ManyToOne), the inverse side(the side that doesn’t own the relationship) is
  annotated and has mappedby that refers to the filed in the class that owns the relationship. 

What is eager/lazy loading?
- lazy loading is a way of loading collections into a memory, with lazy loading collections are not loaded into the memory,
  instead they are wrapped into the proxies (placeholder objects), lazily loaded collection items are not
  initialized and are not loaded into the memory
- eager loading loads collections into the memory

What is JPQL?
- Java Persistence Query Language is an object oriented query language that is used to make queries
  against entities stored in a relational database.

name 2 main types of queries
- native queries
- static query(named)

What is @NamedQuery, where do we place it?
- @NamedQuery is an annotation that is used to define a static query, it is placed on the entity class to which this
  query pertains
  
What is native query?
- queries that are defined in the repository and created using SQL

name 2 types of parameters in JPQL, what symbols each one uses?
- Named parameters (:name)
	public Country getCountryByName(EntityManager em, String name) {
	    TypedQuery<Country> query = em.createQuery(
		"SELECT c FROM Country c WHERE c.name = :name", Country.class);
	    return query.setParameter("name", name).getSingleResult();
	  } 

- ordinal/positional parameters (?index)
	  public Country getCountryByName(EntityManager em, String name) {
	    TypedQuery<Country> query = em.createQuery(
		"SELECT c FROM Country c WHERE c.name = ?1", Country.class);
	    return query.setParameter(1, name).getSingleResult();
	  } 

What is pagination?
- separating a collection of data into pages

What is JOIN FETCH in JPA?
- Join Fetch in queries initializes (eagerly loads) the associated entity or collection of entities.

What nuances we need to know about JOIN FETCH? (you run a query without success, despite entities being present in the DB?)


What is spring data JPA?
- Spring Data JPA allows to create automatic repository and no implementation is needed by the developer. 
  To enable Spring automatic repository create a repository interface for your entity class that has to extend JpaRepository.
  
How to configure JPA in Spring without Spring Data JPA: describe all needed beans.
- To be able to use JPA with Spring an entity manager factory bean should be defined in Spring application context.
  EntityManagerFactory gives you an instance of an EntityManager. 
- You will need the following beans to be defined in your configuration file:
	- DataSource to connect to database
	- JpaVendorAdapter provides specifics for EntityManager about the particular JPA implementation (Hybername or other)
	- LocalContainerEntityManagerFactoryBean - provides an EntityManager bean  which will scan 
	  the model package for classes that are annotated with @Entity
	-  PlatformTransactionManager - TransactionManager
	- BeanPostProcessor - enables persistence exception translation
	- PersistenceAnnotationBeanPostProcessor 
	
  
## Transactions

What is a transaction?
- Unit of work,that cannot be partially completed, if one of the task cannot be completed, the transaction is rolled back.

How does spring handle transactions?
- through AOP

What 2 main types of transaction do we have?
- Declarative - using annotations within the code itself.
- Programmatic - developer writes code to manage transactions

What is JTA transaction manager? When do we need it? Name 2 JTA transaction managers.
JTA transaction manager manages global transactions (Bitronix, Atomikos)

2 main ways to manage transactions?
- global (more than one resource/db)
- local (one resource/db)

Describe Java exception hierarchy, what are checked/unchecked exceptions?
- There are checked and unchecked exceptions in Java, checked exceptions are checked by JVM and compiler.
  unchecked are not checked by JVM and thrown during run time.

In spring declarative transactions, when is transaction marked for rollback?
- When an exception is thrown

How to specify Tx isolation level in Spring? Why do we specify it? Give an example.

@Transactional(isolation=Isolation.READ_COMMITTED)
We specify isolation level to determine what data our transaction can see, with READ_COMMITED isolation
level our transaction will see committed changes only (no dirty reads).

For example, if we are calculating an average of something, we want to make sure we are basing it on the data 
that has been commited in order to get an accurate result.


How to specify Tx propagation level in spring, why do we specify it? give an example.
- @Transactional(Propagation=REQUIRES_NEW)

Explain, and give examples of, REQUIRES_NEW, REQUIRED, and SUPPORTS propagation levels.
- REQUIRED_NEW suspends transaction of the method it was called from and starts a new transaction, the suspended 
method is continued after the transaction is completed.
- REQUIRED - if a tx method A is called from a tx method B, method A joins B's transaction. If method B is not transactional,
  method A starts its own transaction.
- SUPPORTS - if a calling method is transactional, the method that is called joins the calling method's transaction. If the 
  calling method is not transactional, the called method doesn't create a new transaction.

What is readOnly property of a Tx, what about timeout property?
- A method annotated with @Transaction(readOnly = true) only can read data from db.
- timeout property is used to determine the time limit of how long a transaction can be run, if transaction is
  not completed in the allotted time limit it's rolled back.
  
What is default Isolation and propagation level in spring transaction?
- Default propagation - REQUIRED
- Default isolation is determined by the database default settings

Present a demo project showcasing Spring transaction details.
- For example, we have a performSale() method in our Service method, that consists of two repository methods: decreaseInventory() and recordSale(). The service method, performSale(), has to be annotated with @Transactional so that it could be completed as a unit of work. Without the @Transactional annotation, there is a chance that this method may be completed only partially (due to exceptions thrown during decreaseInventory() and recordSale() methods), which doesn't make sense from a business logic point of view. The reason of this behaviour is Spring default propagation level REQUIRED.
All repository methods are transactional by default with REQUIRED propagation level, so when they are called from a
non-transactional method, they create their own transaction. This is why it's important to make the service method
transactional so that repository methods could join the service transaction and as a result the whole service method
could as a unit of work.

How to configure beans needed for TX support in spring?

- In order to enable transactions in your application, annotate your java configuration class with
  @EnableTransactionManagement, and define transactinManager bean:
	@Bean
	public JpaTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {
		JpaTransactionManager transactionManager = new JpaTransactionManager();
		transactionManager.setEntityManagerFactory(entityManagerFactory);
		return transactionManager;
	}
	
What role does repository play in spring transactions?
Repository is a place where transactions begin.

What role does service play in spring transactions? Give an example.
Service calls transactional methods from repository.


## MVC

What is servlet?
- is a program that handles HTTP requests and responses

What 2 main abstractions are used with servlet?
- web context and core context

What is servlet container? One example of.
- is a web server that maintains a lifecycle of a servlet, Tomcat is a servlet container

What is JSP?
- JavaServer Pages allows to create dinamic web pages.

What is dispatch servlet?
- DispatcherServlet receives HTTP requests and responses. It is responsible for routing the requests.

Who creates dispatch servlet?
Spring creates DispatcherServlet you just need to configure it with DiscpatchServletIntializer

What is AbstractAnnotationConfigDispatcherServletInitializer?
- It is an implementation of WebApplicationInitializer interface that is used to start our web application

What 3 main actions does AbstractAnnotationConfigDispatcherServletInitializer perform?
- configures DispatcherServlet (identifies a path DispatcherServlet will be mapped to)
- creates core context and loads it's beans
- creates web context and loads it's beans

How does container find AbstractAnnotationConfigDispatcherServletInitializer?
- Servlet container (Tomcat) looks for any classes that implement ServletContainerInitializer interface,
  AbstractAnnotationConfigDispatcherServletInitializer implements WebApplicationInitializer which implements 
  ServletContainerInitializer.

What 2 main contexts are need for SpringMVC?
- core context and web context

What is the relationship between 2 main contexts in SpringMVC?
- core and web context can access each other's beans

What functionality logically belongs to the root(core) context? Name a few bean types that live there.
- application business logic beans and persistence layer beans. (Repository, Service Beans)

What functionality logically belongs to the web context? Name a few bean types that live there.
- controller, view beans and all beans related to Web component of the application.

How to configure web context? 

	@Configuration
	@EnableWebMvc
	@ComponentScan("web")          // web related beans
	public class WebConfig extends WebMvcConfigurerAdapter {}
	

What is view resolver? 
- View Resolver is a bean that maps view names with actual views and renders views.

What is servlet filter? How is it similar to AOP?
- With a servlet filter you can intercept http requests.

Describe request flow through SpringMVC framework, in as much details as possible, draw a diagram.
- DispatcherServlet receives a requests, and with a help of hendler mapping it sends the request to the appropriate controller. Controller processes the request and returns a view. ViewResolver maps a view name with an actual view and returns that view.

What is a controller?
- Controller is a class that processes the http requests and returns view names.

How to configure a controller? What annotation can we have in a controller?
- @Controller

What is ui.model?
- is ui.model holds an object that is passed from a controller into a view 

What is a view template?
- is an incomplete view that is completed with dynamically rendered data

What is a templating engine.
- View template engine allows to organize view pages and get rid of redundant code.

How is ui.model used with templating engines?
- ui.model used to populate dynamically rendered parts of the view template

What 2 main http methods do you know? 
- GET, POST

What is the difference between 2 main http method?
- GET – retrieves data and has no other effect
- POST – server accepts and processes data; commonly used with forms


## REST

What is REST?
- REST (Representational State Transfer) Web Service - transferring the state of resources (usually in JSON format) from a server to a client or vice versa.

What do we mean by 'state'
- "state" refers to the state of the resource (application entities)

Who are the main users of the REST?
- other applications

what is http request header?
- it key value pairs that contain information about the browser, data, and page content

What is a content negotiation?
- is one of the ways to transform the data to be transferred into a desired format. With content negotiation there is a 
view for each type of format (for example, a view in html and the same view in JSON).

What is message conversion?
- is another way to transform the data into a desired format. There is a message converter in Spring
  that translates an object returned from the controller into a desired format to be served to the client.
  (data produced by  the controller is transformed into a desired type)

What role does view resolver play in message conversion in REST?
- none, view resolver is used in a content negotiation not message conversion

What 2 main roles exist in REST web service?
- client and server

What does it mean to be an endpoint?
- it means you are a server that receives client requests and sends responses 
  (endpoint is URL where REST clients send requests)

What does it mean to be an endpoint consumer?
- REST clients send their requests to URLs(server endpoints) and receive data.
  (Spring RestTemplate gets and posts data)

What is a REST template?
Spring RestTemplate converts received data into an object

How to use REST template to GET resource?
- Result[] response = new RestTemplate().getForObject(getAllUrl, Product[].class);

How to use REST template to POST resource? What does it mean to post a resource?
- RestTemplate().postForObject(postUrl, c, Contact.class)
To post a resource means to create a resource on that URL(endpoint)

## Security

What 2 main approaches does spring security use to secure our applications?
- web request level (Servlet filter)
- method level

What is an authority?
- authority or in other words user role(ADMIN, VISITOR) determines what resources the user should have access to

What is a cookie?
- data stored(key value pairs) about a user on the client-side (browser)

What is a session?
- data stored across multiple http requests on a servers side. A user is assigned an id which is send with each request
to the server in order to identify the user.

What 2 main interfaces help spring security understand a user based on the username? 
- UserDetails and UserDetailsService

How can we implement these 2 main interfaces in our application?
- User class implements UserDetails interface (this is a class that represents User)
- UserService class implements UserDetailsService (this is a class that works with UserRepository and responsible for 
  user persistence operations)

What is a password encoder?
- a bean that encodes user's password so it could be saved in db

Describe main url routes used by spring security
- /login and /logout for default log in and log out pages

What is a success/failure handler?
- success/failure handler determine where user is redirected on successful/failed login and logout

How to utilize spring security in views? 
- we can have different parts of view rendered based on user role

Explain each line of the following code:

	@Configuration//states that this is a config file
	@EnableWebSecurity// enables web security
	public class SecurityConfig extends WebSecurityConfigurerAdapter{

		@Autowired
		UserDetailsService userService; // userService autowired

		@Bean//declaring passwordEncoder bean
		public PasswordEncoder passwordEncoder() {
			return new BCryptPasswordEncoder();
		}

		@Autowired// wires userService and passwordEncoder into Spring Security
		public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
			auth.userDetailsService(userService).passwordEncoder(passwordEncoder());
		}

		@Override// overrides request level default security, allows access to this URL to all users
		public void configure(WebSecurity web) throws Exception {
			web.ignoring().antMatchers("/assets/**");
		}


		@Override
		protected void configure(HttpSecurity http) throws Exception {
			http
				.authorizeRequests()
				.antMatchers("/admin/**").hasRole("ADMIN") // specifies what roles can have access to this url
				.antMatchers("/**").hasAnyRole("ADMIN", "USER")//specifies what roles can have access to this url
			.and()
				.formLogin()
				.loginPage("/login")//overrides default login page
				.permitAll()//gives access to log in page to all users
			.successHandler(
		(request, response, authentication) -> response.sendRedirect("/main"))//where to redirect on successful login
			.failureHandler(
		(request, response, authentication) -> response.sendRedirect("/login"))//where to redirect on failed login
			.and()
				.logout().logoutRequestMatcher(new AntPathRequestMatcher("/logout"))//use /logout to logout
				.logoutSuccessUrl("/").deleteCookies("JSESSIONID")//destroy session id when logged out
				.invalidateHttpSession(true)//destroy session
			.and()
				.csrf();//enable default csrf tokens on all forms
		}
	}

What is method security?
- is security settings on a method level

What does this do:

	@Secured({"ROLE_ADMIN"})
	- sets up security on a method, this method is for users with role admin
	
Explain each line of this code:

	@Configuration// indicates that this is a config class
	@EnableGlobalMethodSecurity(securedEnabled=true) //enables method security
	public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {}
	

