## JavaEE to Quarkus - Examples

### 1. JMS to Smallrye reactive
- Abstract: Even though there exists an extension for using JMS in Quarkus, it is not _officially_ supported. The official position and recommendation of the Quarkus team is to migrate to Quarkus reactive, implemented by the Smallrye reactive library.
- Applications to test: helloworld-mdb and cmt
- Rules:
    - jms-to-reactive-quarkus-00000
        - Checks for unwanted JMS dependencies that need to be removed.
    - jms-to-reactive-quarkus-00010
        - Checks for the `@MessageDriven` EJB, which is not supported (EJBs are in general not supported in Quarkus)
    - jms-to-reactive-quarkus-00020 (❌)
        - This one would be interesting, but its konveyor version is probably going to be too broad, due to the lack of more specific matching in annotations in konveyor.
    - jms-to-reactive-quarkus-00030
        - This rule is quite interesting to try with an LLM. The `@Queue` annotation is a JMS annotation and therefore not supported, and must be switched for an `@Emmiter`. An example is provided in the message, so the LLM might be able to work something out on its own.
    - jms-to-reactive-quarkus-00050
        - Simple javax to jakarta namespace change

### 2. Persistence to Quarkus
- Abstract: The persistence API of JavaEE provides all sorts of utilities for accessing databases in an object oriented manner (object-relational mapping and such). It is [100% supported](https://docs.google.com/spreadsheets/d/1Xt62NAoga6ZXjR3y0LsRc0npgxwSyQFZ9TlpzYmRdoY/edit#gid=0&range=C15), but still needs to be migrated.
- Applications to test: see each rule
- Rules:
    - persistence-to-quarkus-00000
        - Applications to test: cmt and bmt
        - The Persistence API in JavaEE has the concept of datasources and persistence units. These are abstractions of the underlying databases for ORM purposes, and they need to be configured. In JavaEE, XML files are used for this, but in Quarkus all this configuration can (and should) be moved to a single, centralized `*.properties` file.
        - This rule simply looks for the existence of the XML configuration rules. I am hoping that the extensive message in the rule will be enough for the LLM to generate a proper solution, but it still needs to be tested.
    - persistence-to-quarkus-00011
        - Applications to test: greeter and 
        - This is a rule to remove an unneeded (and illegal) `@Produces` annotation on the `@EntityManager`. Curious to see if the LLM will suggest to add the Qualifier, as explained in the message.




