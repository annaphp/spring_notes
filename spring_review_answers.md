# Spring review questions

## Core Spring

What is spring
- Spring is an application framework which main features are dependency injection and aspect oriented programming.

Why use spring over regular Java
 - because of dependecy injections that Spring offers
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
many side should have a foreign key collumn reffering to id of one side

Describe how to model many-to-many relationship in the DB
there should be a third table created to store many-to-many relationship(table one ids and table two ids)

Describe Spring's approach to JDBC, name key interfaces/classes


What is a repository?

What is a datasource? How is it different from drivermanager?

Describe steps to configure JDBC based repository in Spring

## JPA

What is JPA?

What is Hibernate? compare Hibernate and JPA

Name key classes/interfaces in JPA

What are entities?

Name entity states

What are value types? (Basic types like String and classes annotated with @Embeddable)

How can an entity store its collection of values types, what about one value type?

Name 2 main types of relationships between entities?

Name 4 main cardinalities.

Name 7 possible combinations of cardinalities and directions. Why there are only 7 and not 8?

What is unique about unidirectional relations, who owns the relation?

What is unique about bidirectional relations? who owns the relations?

How do we mark an owning side in a bidirectional relation? What is inverse side?

What is eager/lazy loading?

What is JPQL?

name 2 main types of queries

What is @NamedQuery, where do we place it?

What is native query?

name 2 types of parameters in JPQL, what simbols each one uses?

What is pagination?

What is JOIN FETCH?

What nuances we need to know about JOIN FETCH? (you run a query without success, despite entities being present in the DB?)

What is spring data JPA?

How to configure JPA in Spring without Spring Data JPA: describe all needed beans.

## Transactions

What is a transaction?

How does spring handle transactions?

What 2 main types of transaction do we have?

What is JTA transaction manager? When do we need it? Name 2 JTA transaction managers.

2 main ways to manage transactions?

Describe Java exception hierarchy, what are checkec/unchecked exceptions?

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
	

