## Coolstore - JavaEE to Quarkus - Sample picks

Legend:
  - 🟢: Can be used, quite interesting
  - 🟡: Can be used, not very interesting
  - 🔴: Cannot be used


### 1. JMS to Smallrye reactive
- Abstract: Even though there exists an extension for using JMS in Quarkus, it is not _officially_ supported. The official position and recommendation of the Quarkus team is to migrate to Quarkus reactive, implemented by the Smallrye reactive library.
- Rules:
    - jms-to-reactive-quarkus-00000 (🔴 - TODO: check in kantra w/deps)
        - Simple dependency swap. We can use it as an example of a dependency swap, but not very interesting on its own. Checks for unwanted JMS dependencies that need to be removed.
    - jms-to-reactive-quarkus-00010 (🟢)
        - Simple annotation swap. Checks for the `@MessageDriven` EJB, which is not supported (EJBs are in general not supported in Quarkus) and tells to use a CDI bean instead.
        - Triggered in:
          - [com.redhat.coolstore.service.OrderServiceMDB](https://github.com/mathianasj/eap-coolstore-monolith/blob/main/src/main/java/com/redhat/coolstore/service/OrderServiceMDB.java)
        - Migrated as:
          - [com.redhat.coolstore.service.OrderServiceMDB](https://github.com/mathianasj/eap-coolstore-monolith/blob/quarkus-migration/src/main/java/com/redhat/coolstore/service/OrderServiceMDB.java#L16)
    - jms-to-reactive-quarkus-00020 (🟢)
        - The windup rule is more specific, but this could potentially work with the LLM. This rule entails an annotation change. There is enough context in the description for the LLM to pick up what is needed, which is basically to turn the JMS config made in `@ActivationConfigProperty` into a queue listener (`@Incoming`).
        - Triggered in:
          - [com.redhat.coolstore.service.OrderServiceMDB](https://github.com/mathianasj/eap-coolstore-monolith/blob/main/src/main/java/com/redhat/coolstore/service/OrderServiceMDB.java)
        - Migrated as:
          - [com.redhat.coolstore.service.OrderServiceMDB](https://github.com/mathianasj/eap-coolstore-monolith/blob/quarkus-migration/src/main/java/com/redhat/coolstore/service/OrderServiceMDB.java#L25)
    - jms-to-reactive-quarkus-00030 (🔴 - not present)
        - This rule is quite interesting to try with an LLM. The `@Queue` annotation is a JMS annotation and therefore not supported, and must be switched for an `@Emmiter`. An example is provided in the message, so the LLM might be able to work something out on its own.
    - jms-to-reactive-quarkus-00040 (🟢)
        - This rule is similar to jms-to-reactive-quarkus-00030, but using a topic instead of a queue.
        - Triggered in:
          - [com.redhat.coolstore.service.ShoppingCartOrderProcessor](https://github.com/mathianasj/eap-coolstore-monolith/blob/main/src/main/java/com/redhat/coolstore/service/ShoppingCartOrderProcessor.java)
        - Migrated as:
          - [com.redhat.coolstore.service.ShoppingCartOrderProcessor](https://github.com/mathianasj/eap-coolstore-monolith/blob/quarkus-migration/src/main/java/com/redhat/coolstore/service/ShoppingCartOrderProcessor.java#L22)
    - jms-to-reactive-quarkus-00050 (🟡)
        - Simple javax to jakarta namespace change
        - Triggered in:
          - [com.redhat.coolstore.rest.CartEndpoint](https://github.com/mathianasj/eap-coolstore-monolith/blob/main/src/main/java/com/redhat/coolstore/rest/CartEndpoint.java)
          - [com.redhat.coolstore.service.ShoppingCartOrderProcessor](https://github.com/mathianasj/eap-coolstore-monolith/blob/main/src/main/java/com/redhat/coolstore/service/ShoppingCartOrderProcessor.java)
          - ...
        - Migrated as:
          - [com.redhat.coolstore.rest.CartEndpoint](https://github.com/mathianasj/eap-coolstore-monolith/blob/quarkus-migration/src/main/java/com/redhat/coolstore/rest/CartEndpoint.java)
          - [com.redhat.coolstore.service.ShoppingCartOrderProcessor](https://github.com/mathianasj/eap-coolstore-monolith/blob/quarkus-migration/src/main/java/com/redhat/coolstore/service/ShoppingCartOrderProcessor.java)

### 2. Persistence to Quarkus
- Abstract: The persistence API of JavaEE provides all sorts of utilities for accessing databases in an object oriented manner (object-relational mapping and such). It is [100% supported](https://docs.google.com/spreadsheets/d/1Xt62NAoga6ZXjR3y0LsRc0npgxwSyQFZ9TlpzYmRdoY/edit#gid=0&range=C15), but still needs to be migrated.
- Rules:
    - persistence-to-quarkus-00000 (🔴 - in this case the properties have been left implicit)
        - The Persistence API in JavaEE has the concept of datasources and persistence units. These are abstractions of the underlying databases for ORM purposes, and they need to be configured. In JavaEE, XML files are used for this, but in Quarkus all this configuration can (and should) be moved to a single, centralized `*.properties` file.
        - This rule simply looks for the existence of the XML configuration rules. I am hoping that the extensive message in the rule will be enough for the LLM to generate a proper solution, but it still needs to be tested.
        - Triggered in:
          - [src/main/resources/META-INF/persistence.xml](https://github.com/mathianasj/eap-coolstore-monolith/blob/main/src/main/resources/META-INF/persistence.xml)
        - Migrated as:
          - [https://github.com/mathianasj/eap-coolstore-monolith/blob/quarkus-migration/src/main/resources/application.properties](https://github.com/mathianasj/eap-coolstore-monolith/blob/quarkus-migration/src/main/resources/application.properties)
    - persistence-to-quarkus-00011 (🔴 - not present, studying the possibility of adding it artificially)
        - This is a rule to remove an unneeded (and illegal) `@Produces` annotation on the `@EntityManager`. Also removes `@PersistenceContext` and tells about the possible need to create a Qualifier for the `EntityManager`.

### 3. Remote EJB to Quarkus
- Abstract: Remote EJBs are Enterprise Java Beans which can be accessed from a different JVM than that on which their application is being run. This other JVM can be in the same virtual/physical machine or in another. They are not supported in Quarkus, and therefore we recommend to replace them with an equivalent REST service.
- Rules:
  - remote-ejb-to-quarkus-00000 (🔴 - waiting for implementation in coolstore)
    - The javax/jakarta `@Remote` annotation indicates the declaration of a Remote EJB. This bean must be transformed into a REST service. In order to do so, several steps must be taken. They are indicated in the description of the rule.
