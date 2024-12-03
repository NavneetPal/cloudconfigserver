# Minimal Spring Cloud Config Server Setup in Spring Boot

To create a minimal Spring Cloud Config Server in a Spring Boot application, follow these steps:

## 1. Add Dependencies

You need to add the `spring-cloud-starter-config` dependency to your `pom.xml` if you're using Maven.

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-config</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

## 2. Enable Config Server in the Application

In your Spring Boot application class (usually the class with `@SpringBootApplication`), add the `@EnableConfigServer` annotation to enable Spring Cloud Config Server functionality.

```java
package com.example.configserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }
}
```

## 3. Add Application Properties

In `src/main/resources/application.yml` (or `application.properties`), configure the Config Server. At a minimum, you'll need to specify the Git repository URI where the configurations are stored.

Example using `application.yml`:

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/yourusername/your-repository.git  # Git repository URI
          clone-on-start: true  # Optional: Clone the Git repo on server startup
```

## 4. Run the Application
After adding the required dependencies, enabling the Config Server, and configuring the Git repository URI, you can run the Spring Boot application as a regular Spring Boot application. This will start the Config Server, and it will automatically serve the configurations from the specified Git repository.

```bash
mvn spring-boot:run
```

## 5. Accessing Configuration
Once the Config Server is running, you can access configuration properties from the Git repository through HTTP requests. For example:

```bash
http://localhost:8888/{application}/{profile}
```

## Where:

- `{application}` is the name of your application (e.g., `myapp`).
- `{profile}` is the Spring profile (e.g., `dev`, `prod`).

For example, to retrieve properties for an application `myapp` in the `dev` profile:

```bash
http://localhost:8888/myapp/dev
```

## 6. (Optional) Additional Configurations
You can further customize your Config Server by adding more properties, such as authentication for the Git repository, setting a branch to use, or using a local file system instead of Git.

For example:

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/yourusername/your-repository.git
          username: your-username
          password: your-password
          searchPaths: configs  # Optional: Subdirectory in Git repository
          branch: main          # Optional: Specify a branch
```

## Summary
This minimal setup is enough to create a functional Spring Cloud Config Server in Spring Boot:

1. Add the necessary dependencies.
2. Enable the Config Server with `@EnableConfigServer`.
3. Configure the Git repository URI in `application.yml`.
4. Run the application to serve configurations over HTTP.





# Spring Cloud Config Server: `clone-on-start` Property

In the context of Spring Cloud Config Server, the `clone-on-start` property specifies whether the Git repository should be cloned when the Config Server starts up. Here’s a detailed explanation:

## `clone-on-start` Property

### Definition:
The `clone-on-start` property, when set to `true`, instructs the Spring Cloud Config Server to clone the specified Git repository at startup. This means that when the server starts, it will create a local copy of the repository specified by the `uri` property.

### Purpose:
- **Fresh Copy:** Cloning on startup ensures that the Config Server has the latest version of the configuration files from the Git repository. This is particularly useful if your configuration files are frequently updated, as it allows the server to serve the most recent configurations without needing to restart or manually refresh.
- **Local Access:** By cloning the repository locally, the Config Server can access configuration files directly from the local filesystem, which can improve performance compared to accessing them over a network each time a request is made.

### Behavior:
- If `clone-on-start` is set to `true`, every time you start your Config Server, it will attempt to clone the repository specified in `uri`.
- If set to `false`, it will not clone the repository on startup. Instead, it will rely on an existing local copy (if available) or throw an error if no local copy exists.

### Example Configuration:
Here’s how you might configure your Spring Cloud Config Server with this property:

```yaml
spring:
  application:
    name: cloudconfigserver
  cloud:
    config:
      server:
        git:
          uri: https://github.com/NavneetPal/spring-app-config.git
          clone-on-start: true
server:
  port: 8888
```

### Considerations:
- **Performance:** Cloning a large repository can take time, so consider whether you need this feature based on your application's requirements and how often your configuration changes.
- **Repository Updates:** If your configurations change frequently and you want to ensure that your application always has access to the latest configurations, setting this property to `true` can be beneficial.
- **Local Changes:** Be aware that if there are local changes in the cloned directory that are not pushed back to the remote repository, those changes could be lost upon cloning again.

## Conclusion
The `clone-on-start` property in Spring Cloud Config Server allows for automatic retrieval of configuration files from a specified Git repository at server startup. This feature is useful for ensuring that your application always has access to the latest configuration settings without requiring manual intervention or additional steps after deployment.
