## Mock Test
## WireMock Test
When we need to do the integration test involving external libraries and apis, since we don't own them, it's not a good
idea to mock them. Instead we should consider to mock servers. If the libraries and the apis provide a mocking servers,
we can go ahead use them. If they don't offer it, we an use WireMock along with JUnit to mock the service calls.
`WireMock` is one of the most popular http mock servers, we can easily create stubs for customers and product service by
setting up mock responses. It can run as part of an application or a standalone process.
## Cucumber Behavior Driven Development
The concept of BDD is to build automated test cases based on system features which are written in the format of user
stories. The most development is done, the more number of test cases will be passed and eventually all test cases passed
upon completion of the development.
The key element is a feature file which is created by users and business analysts, it describes the system features in natural human language. 
Developers then build test logic in program code and map to each statement in the feature files to form automated test cases.
To execute test cases, we can make use of Cucumber, which is a popular testing framework for BDD, to bind feature files and program code together and run the test cases.
![Cucumber BDD Workflow](bdd.jpeg)
### Feature Definition
System requirements in the feature file are in a pattern of Given-When-Then. The pattern is based on the syntax called Gherkin, 
it defines the precondition in Given statement, action to be taken in When statement and the expected system behavior in Then statement.
Here is a simple example to specify the behavior of Google search feature. Such a format clearly describes system features in a natural human readable language.
```
Scenario: Search for BDD
Given A browser is opened at Google homepage
When I enter ‘BDD’ into the search bar and submit
Then Search result is shown with links related to ‘BDD’
```
If you would like to provide more sample data, then you can put them in the Examples section. 
Cucumber will then run the test case for every record line under the Examples section (except for the 1st header line). 
The example below will run the test case three times for each line in the Examples section.
```
Scenario Outline: Search for keywords
Given A browser is opened at Google homepage
When I enter ‘<keyword>’ into the search bar and submit
Then Search result is shown with links related to ‘<keyword>’
Examples:
| keyword     |
| BDD         |
| Cucumber    |
| Automation  |
```
The use of Given-When-Then pattern is beneficial to the development process as it keeps all user requirements in a consistent format and makes the requirements easy to understand.
Once the feature file is ready, developers can start building the technical implementation for each step in the requirement. 
For example, developers might write code to instruct Chrome browser to perform steps using Selenium according to Given and When clauses, 
then verify the search result as specified in Then clause.
## Spring Boot BDD Utilizing WireMock and Cucumber
1. add cucumber dependencies to `build.gradle.kts` file
```
testImplementation("io.cucumber:cucumber-java:7.3.4") // this might be an implementation
testImplementation("io.cucumber:cucumber-junit:7.3.4")
testImplementation("io.cucumber:cucumber-spring:7.3.4")
```
3. add WireMock dependencies to `build.gradle.kts` file
```
testImplementation("com.github.tomakehurst:wiremock-jre8:2.33.2")
```
## custom deserializer using Jackson 
create a Deserializer class extends jackson StdDesdrializer and override deserialize(JsonParser p,
DeserializationContext ctxt) method.
- To retrieve the root node, call `JsonNode jsonNode = p.getCodec().readTree(p)`
- To retrieve the node as an array, call `ArrayNode arrayNode = (ArrayNode)jsonNode.get("<nodeName>")`, then iterate the
  arrayNode
## Web Client for Rest Service Call
**WebClient** is an interface representing the main entry point for performing web requests.
It was created as part of the Spring Web Reactive module and will be replacing the classic RestTemplate in these scenarios. 
In addition, the new client is a reactive, non-blocking solution that works over the HTTP/1.1 protocol.
It's important to note that even though it is, in fact, a non-blocking client and it belongs to the spring-webflux library,
the solution offers support for both synchronous and asynchronous operations, making it suitable also for applications running on a Servlet Stack.
his can be achieved by blocking the operation to obtain the result. Of course, this practice is not suggested if we're working on a Reactive Stack.
Finally, the interface has a single implementation, the DefaultWebClient class.
1. create an instance
Here I want to create a custom **WebClient** with longer connection/read/write timeout instead default 30 seconds, to
accomplish this, I will create a `reactor.netty.http.client.HttpClient` to pass in for the **WebClient** creation.
- create a `HttpClient` with timeout of 15 minutes (it's not a good idea to make this long, but if there are some
  database connection takes longer, this is the only option)
```
HttpClient httpClient =
        HttpClient.create().option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 1000 * 60 * 15)
            .responseTimeout(Duration.ofMinutes(15))
            .doOnConnected(conn -> conn.addHandlerLast(new ReadTimeoutHandler(15, TimeUnit.MINUTES))
                .addHandlerLast(new WriteTimeoutHandler(15, TimeUnit.MINUTES)));
```
- create a `ExchangeStratety` with max buffer size (the best solution to use stream if the external call supports)
```
 // increase the buffer size to be 16M
    int size = 16 * 1024 * 1024;
    ExchangeStrategies strategies = ExchangeStrategies.builder()
        .codecs(codecs -> codecs.defaultCodecs().maxInMemorySize(size))
        .build();
```
- build the `WebClient` using the `HttpClient` created above
`WebClient webClient WebClient.builder().clientConnector(new ReactorClientHttpConnector(httpClient)).exchangeStrategies(strategies).build();`
2. make a request
- prepare the request - define the method
Here a post request is created
`UriSpec<RequestBodySpec> uriSpec = client.post();`
- prepare the request - define the url
Here the url is specified as a string
`RequestBodySpec bodySpec = uriSpec.uri("http://localhost:5000")`
- prepare the request - define the body
The request body uses BodyInserters to build the body, under the hood the Jackson de/serializer is used to handle the
de/serialization between Java and Json object.
```
User user = new User();
RequestHeadersSpec<?> headersSpec = bodySpec.bodyValue(user);
```
- prepare the request - define the headers
```
ResponseSpec responseSpec = headersSpec.header( HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
  .accept(MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML)
  .acceptCharset(StandardCharsets.UTF_8)
  .ifNoneMatch("*")
  .ifModifiedSince(ZonedDateTime.now())
  .retrieve();;
```
3. handle the response
The response can be atrieved in a fluent way with exception handling and object deserialization.
```
User newUser = new User();
Mono<User> userMono = webClient
            .post()
            .uri("http://localhost:5000") // service url
            .header("Content-Type", "application/json")
            .header("Authorization", "Bear <tokan>") // more headers can be chained
            .bodyValue(newUser) // java object can be serialized into http body
            .accept(MediaType.APPLICATION_JSON)
            .retrieve()
            .onStatus(HttpStatus.INTERNAL_SERVER_ERROR::equals, response -> response.bodyToMono(String.class).map(CustomServerErrorException::new))
            .onStatus(HttpStatus.BAD_REQUEST::equals, response -> response.bodyToMono(String.class).map(CustomBadRequestException::new))
            .bodyToMono(User.class);
User user = userMono.block();
```
4. work with WebTestClient
The WebTestClient is the main entry point for testing WebFlux server endpoints. It has a very similar API to the WebClient, 
and it delegates most of the work to an internal WebClient instance focusing mainly on providing a test context. 
The DefaultWebTestClient class is a single interface implementation.  
The client for testing can be bound to a real server or work with specific controllers or functions.
- bind to a server
To complete end-to-end integration tests with actual requests to a running server, we can use the bindToServer method:
```
WebTestClient testClient = WebTestClient
  .bindToServer()
  .baseUrl("http://localhost:5000")
  .build();
```
- bind to a router
We can test a particular RouterFunction by passing it to the bindToRouterFunction method:
```
RouterFunction function = RouterFunctions.route(
  RequestPredicates.GET("/resource"),
  request -> ServerResponse.ok().build()
);

WebTestClient
  .bindToRouterFunction(function)
  .build().get().uri("/resource")
  .exchange()
  .expectStatus().isOk()
  .expectBody().isEmpty();
```
- bind to a web handler
The same behavior can be achieved with the bindToWebHandler method, which takes a WebHandler instance:
```
WebHandler handler = exchange -> Mono.empty();
WebTestClient.bindToWebHandler(handler).build();
```
- bind to an application context
A more interesting situation occurs when we're using the bindToApplicationContext method. 
It takes an ApplicationContext and analyses the context for controller beans and @EnableWebFlux configurations.
If we inject an instance of the ApplicationContext, a simple code snippet may look like this:
```
@Autowired
private ApplicationContext context;

WebTestClient testClient = WebTestClient.bindToApplicationContext(context)
  .build();
```
- bind to a controller
A shorter approach would be providing an array of controllers we want to test by the bindToController method. 
Assuming we've got a Controller class and we injected it into a needed class, we can write:
```
@Autowired
private Controller controller;

WebTestClient testClient = WebTestClient.bindToController(controller).build();
```
- make a request
After building a WebTestClient object, all following operations in the chain are going to be similar to the 
WebClient until the exchange method (one way to get a response),
which provides the WebTestClient.ResponseSpec interface to work with useful methods like the expectStatus,
expectBody, and expectHeader:
```
WebTestClient
  .bindToServer()
    .baseUrl("http://localhost:8080")
    .build()
    .post()
    .uri("/resource")
  .exchange()
    .expectStatus().isCreated()
    .expectHeader().valueEquals("Content-Type", "application/json")
    .expectBody().jsonPath("field").isEqualTo("value");
```

