# Case Study - WireMock
In this example, we document a way to intercept requests to a targeted API and reuse the mappings for integration tests.

The official documentation can be found at [![](./case-study-wire-mock-0.png)Record and Playback](https://wiremock.org/docs/record-playback/)

## ![:blue_book:](./1f4d8.png) Instructions

1.  Install WireMock in your machine: `$ brew install wiremock-standalone`
    
2.  Open the java test resource folder in your project: `$ cd src/test/resources`
    
3.  Start WireMock using the proxy mode intercepting requests, the default port will be **8080**: `$ wiremock --enable-browser-proxying --trust-all-proxy-targets --record-mappings --verbose`
    
4.  \[OPTIONAL\] If the request you want to intercept is using HTTPS you have to install the WireMock certificate first, this is one example for doing so in the JVM truststore:
    
    1.  `$ curl -s "http://localhost:8080/__admin/certs/wiremock-ca.crt" -o wiremock-ca.crt`
        
    2.  `$ sudo keytool -importcert -file wiremock-ca.crt -alias wiremock-ca -storepass changeit -cacerts`
        
5.  Make requests to your targeted API using WireMock as a proxy so it will start recording the mappings, example: `$ curl --proxy http://localhost:8080 http://time.jsontest.com`
    
6.  Now look at your test resources folder and you should see something like this:
    
```
src/test/resources
├── __files
│   └── body-(root)-1FmFI.json
└── mappings
    └── mapping-(root)-1FmFI.json
```
    
7.  You now can include WireMock as a test dependency in your project and utilize the recorded mappings for your integration tests, WireMock will utilize any compliant Json in the `src/test/resources/mappings` folder to serve requests:
    
a.  
    
```
<dependency>
    <groupId>com.github.tomakehurst</groupId>
    <artifactId>wiremock-jre8</artifactId>
    <version>${wiremock-jre8.version}</version>
    <scope>test</scope>
</dependency>
```
        
b.  
    
```
@WireMockTest(httpPort = 8888)
public class MyIntegrationTestSetIT {
...
}
```
        

> You can derive it to multiple scenarios according to your use cases. Remember to configure your http client to make calls to `http://localhost:8080` in your integration tests.

  

*   [Create XRay Dashboard](/wiki/spaces/ES/pages/4219707258/Create+XRay+Dashboard)
    
*   [Create Bugs Dashboard](/wiki/spaces/ES/pages/4295266386/Create+Bugs+Dashboard)
    
*   [Importing a new certificate into the Java Trust Store for BaaS](/wiki/spaces/ES/pages/4239753883/Importing+a+new+certificate+into+the+Java+Trust+Store+for+BaaS)
    
*   [Istio Egress mTLS on Azure](/wiki/spaces/ES/pages/4259840848/Istio+Egress+mTLS+on+Azure)
    
*   [How to Create a Filter](/wiki/spaces/ES/pages/4257873926/How+to+Create+a+Filter)