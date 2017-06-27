# Spring review questions

## Core Spring

What is spring
- Spring is an application framework which main features are dependency injection and aspect oriented programming.

Why use spring over regular Java
 - because of a dependecy injection that Spring offers
 
What is DI?
 - DI stands for a depenedency injection which is a way to provide required refferences (dependencies) to the objects
   in your application.

3 ways to configure spring container, describe each in detail
 - Java Cofiguration - a Java configuration class is created annotated with @Configuration where beans are created and 
   dependancies are defined.(Use @Bean to declare a bean)
 - Autowiring and Component Scan - to enable autowiring and component scan, create a configuratin java class that should
   be annotated with @Configuration and @ComponentScan (you can also create xml config file and enable componentScan in
   there). @Component annotation is used to label all classes for wich a bean should be created by Spring. To provide a 
   reference to an object (DI) @Autowired is used.
 - XML configuration - xml file is used to configure srping container, create an xml file and declare beans in there using
   <bean> element. 

describe default bean naming in all 3 configuration types, and how to override these defaults
 - Java Configuration - default name is @Bean annotated method name,  to override default name  - @Bean(name="newName")
 - Autowiring and ComponentScan - default name is a class name in lowercase, to override default name - @Component("newName")
 - XML configuarion - default name is a class name#number, to override default name use id attribute - <bean id="newName"   class="MyClass"/>

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
- Inner join - retrives rows form table A and table B that have matching values in both tables. For example:
	SELECT books.name, authors.name FROM books, authors WHERE authors.id = books.author_id;
- Left Join - retrives all rows form table A even if there are no matching rows in table B. For example:
	SELECT books.name, authors.name FROM books LEFT JOIN authors ON books.author_id = authors.id;
- Right Join - retrives all rows from table B even if there are no matching rows in table A. For example:
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
 - dirty read - your transaction sees uncommited changes
 - non-repeatable read - when you read the same row in your transaction twice and get different results
 - phantom read - when you calculate a value based on the number of rows in your transaction twice and get a different result
   because extra rows were added by another user

Describe transaction isolation levels
 - Read Uncomitted - allows dirty read, non-repetable reads and phantom reads
 - Read Commited - allows non-repetable and phantom reads
 - Repetable read - allows phantom reads only
 - Serializable - doesn't allow dirty reads, non-repetable reads and phantom reads

Describe how we can model many-to-one relationship in the DB
 - many side should have a foreign key collumn reffering to id of one side

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
  and is available for other requiests) whereas with drivermanager you need to open/close connections.
  
Describe steps to configure JDBC based repository in Spring
- To enable JdbcTemplate in your application the following steps have to be completed:
	- set up required dependencies
	- define Datasource bean in Spring dependency config file
	  DataSource represents a database, it has a connection pool which contains a set of 
	  connections. DataSouce requires jdbc driver, database url, database username and password, connection pool initia
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

JPA is an interface (a standard for ORM), Hibernate is an implementation that satisfies JPA standarts.

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
- @NamedQuery is an annotation that is used to define a static query, it is planced on the entity class to which this
  query pertains
  
What is native query?
- queries that are defined in the repository and created using SQL

name 2 types of parameters in JPQL, what simbols each one uses?
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

2 main ways to manage transactions?
- global (more than one resource/db)
- local (one resource/db)

Describe Java exception hierarchy, what are checked/unchecked exceptions?
- There are checked and unchecke exceptions, checked exceptions are checked by JVM and compiler.
  unchecked are not checked by JVM and thrown during run time.

In spring declarative transactions, when is transaction marked for rollback?

How to specify Tx isolation level in Spring? Why do we specify it? Give an example.

How to specify Tx propagation level in spring, why do we specify it? give an example.

Explain, and give examples of, REQUIRES_NEW, REQUIRED, and SUPPORTS propagation levels.

What is readOnly property of a Tx, what about timeout property?

What is default Isolation and propagation level in spring transaction?

Present a demo project showcasing Spring transaction details.

How to configure beans needed for TX support in spring?

What role does repository play in spring transactions?

What role does service play in spring transactions? Give an example.


## MVC

What is servlet?

What 2 main abstractions are used with servlet?

What is servlet container? One example of.

What is JSP?

What is dispatch servlet?

Who creates dispatch servlet?

What is AbstractAnnotationConfigDispatcherServletInitializer?

What 3 main actions does AbstractAnnotationConfigDispatcherServletInitializer perform?

How does container find AbstractAnnotationConfigDispatcherServletInitializer?

What 2 main contexts are need for SpringMVC?

What is the relationship between 2 main contexts in SpringMVC?

What functionality logically belongs to the root(core) context? Name a few bean types that live there.

What functionality logically belongs to the web context? Name a few bean types that live there.

How to configure web context? 

	@Configuration
	@EnableWebMvc
	@ComponentScan("web")          // web related beans
	public class WebConfig extends WebMvcConfigurerAdapter {}
	

What is view resolver? 

What is servlet filter? How is it similar to AOP?

Describe request flow through SpringMVC framework, in as much details as possible, draw a diagram.

What is a controller?

How to configure a controller? What annotation can we have in a controller?

What is ui.model?

What is a view template?

What is a templating engine.

How is ui.model used with templating engines?

What 2 main http methods do you know? 

What is the difference between 2 main http method?

## REST

What is REST?

What do we mean by 'state'

Who are the main users of the REST?

what is http request header?

What is content negotiation?

What is message conversion?

What role does view resolver play in message conversion in REST?

What 2 main roles exist in REST web service?

What does it mean to be an endpoint?

What does it mean to be an endpoint consumer?

What is a REST template?

How to use REST template to GET resource?

How to use REST template to POST resource? What does it mean to post a resource?


## Security

What 2 main approaches does spring security use to secure our applications?

What is an authority?

What is a cookie?

What is a session?

What 2 main interfaces help spring security understand a user based on the username? 

How can we implement these 2 main interfaces in our application?

What is a password encoder?

Describe main url routes used by spring security

What is a success/failure handler?

How to utilize spring security in views? 

Explain each line of the following code:

	@Configuration
	@EnableWebSecurity
	public class SecurityConfig extends WebSecurityConfigurerAdapter{

		@Autowired
		UserDetailsService userService;

		@Bean
		public PasswordEncoder passwordEncoder() {
			return new BCryptPasswordEncoder();
		}

		@Autowired
		public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
			auth.userDetailsService(userService).passwordEncoder(passwordEncoder());
		}

		@Override
		public void configure(WebSecurity web) throws Exception {
			web.ignoring().antMatchers("/assets/**");
		}


		@Override
		protected void configure(HttpSecurity http) throws Exception {
			http
				.authorizeRequests()
				.antMatchers("/admin/**").hasRole("ADMIN") // order is important
				.antMatchers("/**").hasAnyRole("ADMIN", "USER")
			.and()
				.formLogin()
				.loginPage("/login")
				.permitAll()
			.successHandler(
		(request, response, authentication) -> response.sendRedirect("/main"))
			.failureHandler(
		(request, response, authentication) -> response.sendRedirect("/login"))
			.and()
				.logout().logoutRequestMatcher(new AntPathRequestMatcher("/logout"))
				.logoutSuccessUrl("/").deleteCookies("JSESSIONID")
				.invalidateHttpSession(true)
			.and()
				.csrf();
		}
	}

What is method security?

What does this do:

	@Secured({"ROLE_ADMIN"})
	
Explain each line of this code:

	@Configuration
	@EnableGlobalMethodSecurity(securedEnabled=true)
	public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {}
	

