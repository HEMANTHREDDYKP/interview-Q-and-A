Maven / POM Dependency Management Interview Q&A
1️⃣ What is a POM file in Maven?

Answer:
POM stands for Project Object Model. It’s an XML file (pom.xml) that contains:

Project metadata (name, version, description)

Build configurations

Dependencies

Plugins and repositories

It tells Maven how to build and manage your project.

2️⃣ What are transitive dependencies in Maven?

Answer:
When you include a dependency, that dependency may itself depend on other libraries.
Maven automatically downloads these indirect dependencies — called transitive dependencies.

Example:
If you add spring-boot-starter-web, it automatically brings in dependencies like spring-web, spring-core, jackson, tomcat, etc.

3️⃣ How do you handle transitive dependency conflicts?

Problem:
Two different dependencies may bring different versions of the same transitive dependency.

Solutions:

Use <dependencyManagement>

Specify the version explicitly so it overrides transitive ones.

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.36</version>
        </dependency>
    </dependencies>
</dependencyManagement>


Use <exclusions>

Exclude unwanted transitive dependency.

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>


Use mvn dependency:tree

To find where the conflict is coming from.

4️⃣ How do you handle circular dependencies?

Problem:
Module A depends on Module B, and Module B depends on Module A.

Solutions:

Refactor design: Move shared logic into a third common module.

In Spring, use @Lazy injection or constructor injection carefully to break circular dependency.

@Component
public class A {
    @Autowired
    @Lazy
    private B b;
}

5️⃣ What if you need two versions of the same dependency?

Answer:
Maven does not allow two versions of the same dependency in a single classpath — it keeps only one (nearest in the dependency tree).

Workarounds:

Shade Plugin (maven-shade-plugin):
Create a “fat JAR” that relocates packages so both versions can coexist.

Isolate modules:
Put each version in separate microservices or classloaders.

⚙️ Spring Framework vs Spring Boot
6️⃣ Why Spring Boot and not just Spring?
Feature	Spring	Spring Boot
Configuration	Manual (lots of XML or Java config)	Auto-configured
Setup	Complex	Simple (one annotation @SpringBootApplication)
Dependency Management	Manual	Starter dependencies (predefined versions)
Embedded Server	No	Yes (Tomcat/Jetty/Undertow)
Production Ready	No	Yes (Actuator, Metrics, Health checks)

Answer:
Spring Boot reduces boilerplate configuration. It auto-configures commonly used beans, manages dependencies through “starters”, and allows quick application development with embedded servers.

7️⃣ What are the main functionalities of the Spring Framework?

Dependency Injection (DI) — Loose coupling using @Autowired, @Component, etc.

Aspect-Oriented Programming (AOP) — Cross-cutting concerns like logging, security.

Transaction Management — Declarative transaction support with @Transactional.

Data Access / ORM Integration — Simplifies JPA/Hibernate integration.

Spring MVC — Web application development framework.

Spring Security — Authentication and authorization.

Spring Test — Simplified integration and unit testing.

💰 Spring Transaction Management Q&A
8️⃣ What is a transaction?

A transaction is a sequence of operations that must all succeed or fail together (ACID: Atomicity, Consistency, Isolation, Durability).

9️⃣ How do you manage transactions in Spring?

Declarative:
Use @Transactional on methods or classes.

@Transactional
public void transferMoney() {
    debit();
    credit();
}


Programmatic:
Manually manage transactions via PlatformTransactionManager.

🔟 What happens if an exception occurs inside a @Transactional method?

By default, runtime exceptions trigger rollback.

Checked exceptions do not trigger rollback unless configured:

@Transactional(rollbackFor = Exception.class)

1️⃣1️⃣ How does Spring implement transactions internally?

Spring uses AOP proxies to wrap the transactional method:

Opens a transaction before the method starts.

Commits if successful.

Rolls back if an exception occurs.

1️⃣2️⃣ Common pitfalls with @Transactional

Calling a @Transactional method from the same class won’t work (proxy not applied).

Must be on public methods (default proxy mode).

If using multiple data sources, you need distributed transaction management (JTA or ChainedTransactionManager).
