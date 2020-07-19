# multitenant-r2dbc-poc

A **multi-tenant**(shared database server, separate schema) reactive microservice based on **Spring Webflux** and **R2DBC** using MySQL as database.
Tenant information is passed as part of URL. The APIs are of the form: `http://localhost:8080/{tenant}/api/**` .

Upon receiving a request, first the tenant is extracted from URL and put in the reactor context. In order to dynamically connect to the tenant specific schema, 
Spring's *RoutingConnectionFactory* has been leveraged which uses a look-up key.

### APIs

* POST /{tenant}/api/employee - Creating new employee
* PUT /{tenant}/api/employee/{id} - Updates given employee
* GET /{tenant}/api/employee/{id}/reportees/direct - Get list of employees who directly report to given employee
* GET /{tenant}/api/employee/{id}/reportees/all - Get list of subordinate employees of given employee
* GET /{tenant}/api/employee/{id} - Get details of an employee

* POST /{tenant}/api/grade - Create or update grade information
* GET /{tenant}/api/grade - Get all grades available



### E-R Model
<img src="https://i.imgur.com/n2ZbGp1.png" width="400" height="200" />

### **Notes** (As of Spring Boot 2.3.1.RELEASE which uses Spring Data R2DBC 1.1.1.RELEASE): 

* There's **no @MappedCollection** in Spring Data R2DBC. Besides that, Spring Data R2DBC doesn't support mapped relationships of any kind.
* By default, the save() method on Repository (extending ReactiveCrudRepository) inspects the identifier property of the given entity. **If the identifier property is null, then the entity is assumed to be new. Otherwise, it is assumed exist** in the datbase.
* If an entity implements **Persistable**, Spring Data R2DBC delegates the new detection to the **isNew(…) method of the entity**.
* If we want to implement the logic for repository layer operations by ourselves, we have 2 main options:

  * Injecting **DatabaseClient** and using methods like *execute*, *delete*, *insert*, *select*, *update* etc
  * Creating a **R2dbcEntityTemplate** from DatabaseClient and using methods like *exists*, *select*, *selectOne*, *count*, *insert*, *update*, *delete* etc

* Currently there is no support for database migrations from Flyway or Liquibase. However there is an opensource Migration Tool made available for R2DBC by Nikita Konev.
  
### Useful Links

* [Official Doc for Spring Data R2DBC](https://docs.spring.io/spring-data/r2dbc/docs/1.1.1.RELEASE/reference/html/#introduction)
* [R2DBC Official MySQL Driver Implementation](https://github.com/mirromutth/r2dbc-mysql)
* [R2DBC Migration Tool](https://github.com/nkonev/r2dbc-migrate)

