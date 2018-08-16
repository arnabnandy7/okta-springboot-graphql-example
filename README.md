# Build a Secure API with Spring Boot and GraphQL
 
This example app shows how to create a Spring Boot API with GraphQL and lock it down with Okta.

Please read [Build a Secure API with Spring Boot and GraphQL]() to see how this app was created.

**Prerequisites:** [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) and an [Okta Developer Account](https://developer.okta.com)

> [Okta](https://developer.okta.com/) has Authentication and User Management APIs that reduce development time with instant-on, scalable user infrastructure. Okta's intuitive API and expert support make it easy for developers to authenticate, manage, and secure users and roles in any application.

* [Getting Started](#getting-started)
* [Links](#links)
* [Help](#help)
* [License](#license)

## Getting Started

To install this example application, run the following commands:

```bash
git clone git@github.com:oktadeveloper/okta-springboot-graphql-example.git
cd okta-springboot-graphql-example
```

Open a terminal and from the OktaGraphQL project directory, start the Spring Boot application using the `./gradlew bootRun` command.

It may take a few seconds to get started. You should see some output that ends like this:

```bash
Tomcat started on port(s): 9000 (http) with context path ''
Started GraphQLToolsSampleApplication in 19.245 seconds (JVM running for 19.664)
```

Leave that terminal window open and open another terminal window. Navigate again to the project root. Use the following command to run our first GraphQL Query:

```bash
http POST http://localhost:9000/graphql/ < json-requests/post1-all-data.json
``` 

### Create an Application in Okta

You will need to create an OIDC Application in Okta to get your settings to perform authentication. 

1. Log in to your developer account on [developer.okta.com](https://developer.okta.com).
2. Navigate to **Applications** and click on **Add Application**.
3. Select **Web** and click **Next**. 
4. Give the application a name, add `http://localhost:8080/login` as a Login redirect URI, and click **Done**.

In the OktaGraphQL project, create a `gradle.properties` file and fill in the following properties:

```properties
oktaClientId=<your client ID>
oktaBaseUrl=<your Okta base URL>
```

In the `src/main/resources/application.yml` file, add the following properties:

```yml
okta:  
   oauth2: 
      issuer: ${oktaBaseUrl}/oauth2/default  
      clientdId: ${oktaClientId}  
      scopes: 'email profile openid'
```

Add the following dependencies to the `build.gradle` file in the OktaGraphQL project. These are the Spring Boot OAuth dependencies and the [Okta Spring Boot starter](https://github.com/okta/okta-spring-boot).

```gradle
compile group: 'com.okta.spring', name: 'okta-spring-boot-starter', version: '0.6.0'  
compile group: 'org.springframework.security.oauth', name: 'spring-security-oauth2', version: '2.3.3.RELEASE'  
compile ('org.springframework.security.oauth.boot:spring-security-oauth2-autoconfigure:2.0.1.RELEASE')
```

Add two annotations (`@EnableResourceServer` and  `@EnableOAuth2Sso`) to the `GraphQLToolsSampleApplication` class. It should look like this:

```java
package com.okta.springbootgraphql;  
  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
import org.springframework.boot.autoconfigure.security.oauth2.client.EnableOAuth2Sso;  
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableResourceServer;  
  
@SpringBootApplication  
@EnableResourceServer  
@EnableOAuth2Sso  
public class GraphQLToolsSampleApplication {  
  
    public static void main(String[] args) {  
        SpringApplication.run(GraphQLToolsSampleApplication.class, args);  
    }  
}
```

Stop your Spring Boot app (if it's still running) and restart it using `./gradlew bootRun`.

Run a query again and you'll see a 401 error.

To get an access token, create a `OktaShowToken/gradle.properties` file and fill in the following properties:

```properties
oktaClientId=<your client ID>
oktaClientSecret=<your client secret>
oktaBaseUrl=<your Okta base URL>
```

Open a terminal, go to the OktaShowToken project root, and run `./gradlew bootRun`.

Once the application completes launching, navigate to `https://localhost:8080`, log in, and copy the resulting access token string. Store the token value in a temporary shell variable:

```bash
TOKEN=<your token value>
```

Execute the request again setting the authorization header. Run the following command from the `OktaGraphQL` directory:

```bash
http POST http://localhost:9000/graphql/ Authorization:"Bearer $TOKEN" < json-requests/post1-all-data.json 
```

And that's it!

## Links

This example uses the following open source libraries:

* [Okta Spring Boot Starter](https://github.com/okta/okta-spring-boot)
* [Spring Boot](https://spring.io/projects/spring-boot)
* [Spring Security](https://spring.io/projects/spring-security)

## Help

Please post any questions as comments on the [blog post](), or visit our [Okta Developer Forums](https://devforum.okta.com/). You can also email developers@okta.com if you'd like to create a support ticket.

## License

Apache 2.0, see [LICENSE](LICENSE).
