# Test Containers
## What is Testcontainers?

It’s a Java library that allows you to bring up docker images during the testing process and use real images of databases, message queues, etc.. instead of mocking or using H2. There are some ready-to-use test containers (Mysql, Kafka,…) but if you can’t find your desired module you can use any custom image that you want.

You can find ready-to-use modules on the [Testcontainers website](https://www.testcontainers.org/ "https://www.testcontainers.org/").

## How to use it?

In this example, we use JUnit 5 (Testcontainers supports JUnit 4 too). To start, we need to add two dependencies to the POM file of the project:

```xml
         <dependency>
             <groupId>org.testcontainers</groupId>
             <artifactId>testcontainers</artifactId>
             <version>1.15.3</version>
             <scope>test</scope>
         </dependency>
         <dependency>
             <groupId>org.testcontainers</groupId>
             <artifactId>junit-jupiter</artifactId>
             <version>1.15.3</version>
             <scope>test</scope>
         </dependency>
```

For this example, we use Mysql and ActiveMQ. For Mysql we need to add another dependency:

```xml
         <dependency>
             <groupId>org.testcontainers</groupId>
             <artifactId>mysql</artifactId>
             <version>1.15.3</version>
             <scope>test</scope>
         </dependency>
```

On top of the test class, we add a `@Testcontainers` annotation:

 ```java
 @SpringBootTest(classes = Application.class)
 @DirtiesContext(classMode = ClassMode.AFTER_CLASS)
 @AutoConfigureMockMvc
 @ActiveProfiles("it")
 @ContextConfiguration(classes = {Application.class})
 @Testcontainers
 @Slf4j
 class ExampleControllerIT {
```

This annotation enables Testcontainers JUnit extension which finds all fields inside the test class annotated with `@Container` and calls their container lifecycle methods.

Now we need to define our containers as variables inside the test class. We should define them as static fields. For Mysql, we have a ready-to-use container:

```java
     @Container
     private static final JdbcDatabaseContainer<?> mysql = new MySQLContainer<>("mysql:5.7.22")
             .waitingFor(Wait.forListeningPort());
```

For ActiveMQ, we need to Define a Generic container:

```java
     @Container
     private static final GenericContainer<?> activemq = new GenericContainer<>(DockerImageName.parse("rmohr/activemq:5.14.0"))
             .withExposedPorts(61616)
             .withPrivilegedMode(true)
             .waitingFor(Wait.forListeningPort());
```

You can set up your docker config in the generic container here.

Testcontainers uses random port mapping so we need to set the URL of Mysql and ActiveMQ after the container is up and running. We can use `@DynamicPropertySource` annotation to config spring boot values:

```java
     @DynamicPropertySource
     static void registerDynamicProperties(DynamicPropertyRegistry registry) {
         String activeMQUrl = String.format("tcp://%s:%d", activemq.getContainerIpAddress(), activemq.getFirstMappedPort());
         registry.add("spring.activemq.broker-url", () -> activeMQUrl);
         registry.add("spring.datasource.url", () -> mysql.getJdbcUrl() + "?useSSL=false");
         registry.add("spring.datasource.username", mysql::getUsername);
         registry.add("spring.datasource.password", mysql::getPassword);
     }
```

This spring annotation waits for the containers to be ready and gives you the ability to add dynamic values to the Environment’s set of PropertySources. Methods annotated with this annotation must be static and have a single `DynamicPropertyRegistry` argument.

Now we can run the test. Make sure your Docker daemon is started. The first run might take forever because it needs to download the images, but the next run will be faster.