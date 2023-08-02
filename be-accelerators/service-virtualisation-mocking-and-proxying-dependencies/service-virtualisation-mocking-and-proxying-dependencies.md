# Service Virtualisation - Mocking and Proxying Dependencies

|     |     |
| --- | --- |
| **Status** | In progress |
| **Impact** | HIGH |
| **Driver** | @Ueslei Lima |
| **Contributors** | @Yash Kapila @Dhielber Manzoli @Giuseppe Deraco @Rafael Nascimento |
| **Informed** | @Luca Mastrini |

## ![:books:](./1f4da.png) Relevant data

Service Virtualization common scenarios in projects:

*   Mobile applications using proxies to modify API responses and test specific behaviours;
    
*   Backend using a Core Bank API simulation to hold control of the development environments with mock data;
    
*   Backend integration tests using stubbed response and verification;
    
*   Web applications mocking Banking Services to run and test angular pages standalone;
    

## ![:blue_book:](./1f4d8.png) Background

Projects have been using multiple solutions for simulating expected system behaviours, whether in the Frontend or in the Backend. A non-unified approach leads to a lack of reusability and extra implementation and configuration effort for the CS teams, where the knowledge about valuable and productive tools remains siloed to a single group. In this initiative, we want to consider one option or at least a pool of options to be adopted by the CS teams that should cover most of the common scenarios implemented in projects.

## ![:rainbow:](./1f308.png) Options considered



|     |     |     |     |     |     |
| --- | --- | --- | --- | --- | --- |
|     |     | **WireMock** | **MockServer** | **Hoverfly** | **Prism** |
| **Website** |     | [![](https://wiremock.org/images/favicon.svg)WireMock - flexible, open source API mocking](https://wiremock.org/) | [![](https://www.mock-server.com/favicon.ico)MockServer](https://www.mock-server.com/) | [Hoverfly by SpectoLabs](https://hoverfly.io/) | [![](https://meta.stoplight.io/favicons/favicon.ico)Prism](https://meta.stoplight.io/docs/prism/) |
| **Pros and cons** |     | ![:plus:](./plus_32.png) Widely adopted by the community with excellent UI providing useful features<br><br>![:minus:](./minus_32.png) Helm charts outdated | ![:plus:](./plus_32.png) Multiple alternatives for execution<br><br>![:minus:](./minus_32.png) Limited UI | ![:plus:](./plus_32.png) Flexible implementation for middleware to extend the functionality<br><br>![:minus:](./minus_32.png) Less traction in the community<br><br>![:minus:](./minus_32.png) Limited UI | ![:plus:](./plus_32.png) Extremely easy to generate stubs based on OpenAPI specs<br><br>![:minus:](./minus_32.png) It doesn’t support Java/JUnit assertions and validations |
| **Features** | Extensible | ![:white_check_mark:](./2705.png) [Java](https://wiremock.org/docs/extending-wiremock/) | ![:white_check_mark:](./2705.png) [Java](https://www.mock-server.com/mock_server/getting_started.html#button_response_class_callback) | ![:white_check_mark:](./2705.png) Multiple Languages | ![:cross_mark:](./cross_mark_32.png) |
| Proxy Requests (With TLS) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) |
| Record & Replay network traffic | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:cross_mark:](./cross_mark_32.png) |
| Mocking and Stubbing | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) |
| Generate Stubs from OpenAPI specs | ![:warning:](./warning_32.png) Via wiremock studio only | ![:cross_mark:](./cross_mark_32.png) | ![:cross_mark:](./cross_mark_32.png) | ![:white_check_mark:](./2705.png) |
| Standalone Server | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) |
| JVM Support (JUnit) | ![:white_check_mark:](./2705.png) [It can run on Android](https://handstandsam.com/2016/01/30/running-wiremock-on-android/) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:cross_mark:](./cross_mark_32.png) |
| Docker Images | ![:white_check_mark:](./2705.png) [ARM and AMD64](https://hub.docker.com/r/wiremock/wiremock) | ![:white_check_mark:](./2705.png) [AMD64 Only](https://hub.docker.com/r/mockserver/mockserver) | ![:white_check_mark:](./2705.png) [ARM and AMD64](https://hub.docker.com/r/spectolabs/hoverfly) | ![:white_check_mark:](./2705.png) [AMD64 Only](https://hub.docker.com/r/stoplight/prism) |
| Helm Chart | ![:cross_mark:](./cross_mark_32.png) | ![:white_check_mark:](./2705.png) | ![:cross_mark:](./cross_mark_32.png) | ![:warning:](./warning_32.png)Not officially maintained |
| Kubernetes Dynamic Service Interception | ![:white_check_mark:](./2705.png) | ![:cross_mark:](./cross_mark_32.png) | ![:cross_mark:](./cross_mark_32.png) | ![:cross_mark:](./cross_mark_32.png) |
| Local CLI | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) |
| Web Dashboard | ![:white_check_mark:](./2705.png) WireMock Studio - Free | ![:warning:](./warning_32.png) Limited - Open Source | ![:warning:](./warning_32.png)Limited - Open Source | ![:cross_mark:](./cross_mark_32.png) |
| REST API | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:cross_mark:](./cross_mark_32.png) | ![:cross_mark:](./cross_mark_32.png) |
| Node Support | ![:cross_mark:](./cross_mark_32.png) | ![:white_check_mark:](./2705.png) | ![:cross_mark:](./cross_mark_32.png) | ![:white_check_mark:](./2705.png) |
| Maven Plugin | ![:cross_mark:](./cross_mark_32.png) | ![:white_check_mark:](./2705.png) | ![:cross_mark:](./cross_mark_32.png) | ![:cross_mark:](./cross_mark_32.png) |
| Testcontainer Module | ![:warning:](./warning_32.png) Only via GenericContainer | ![:white_check_mark:](./2705.png)[![](https://java.testcontainers.org/favicon.ico)Mockserver Module - Testcontainers for Java](https://www.testcontainers.org/modules/mockserver/) | ![:warning:](./warning_32.png) Only via GenericContainer | ![:warning:](./warning_32.png) Only via GenericContainer |
| Open Source | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) | ![:white_check_mark:](./2705.png) |
| Project Activity | ![:white_check_mark:](./2705.png) 5.1k starts, 1.3k forks on GH, Docker **1M+** | ![:white_check_mark:](./2705.png) 3.8k stars, 956 forks on GH, Docker Pulls **50M+** | ![:warning:](./warning_32.png) 1.9k stars, 186 forks on GH, Docker Pulls **5M+** | ![:white_check_mark:](./2705.png) 3.1k starts, 258 forks on GH, Docker Pulls **1M+** |

## ![:white_check_mark:](./2705.png) Action items

@Ueslei Lima To validate the extensibility of the Mocking frameworks to give flexibility to the Backend developer to implement complex scenarios.

@Dhielber Manzoli to validate use cases for QA’s and update the relevant data section here.

@Giuseppe Deraco to validate if [WireMock Studio](https://wiremock.org/studio/docs/getting-started/docker/ "https://wiremock.org/studio/docs/getting-started/docker/") can be used as an alternative to Proxyman for Mobile.

## ![:star2:](./1f31f.png) Outcome

The decision is to utilize **WireMock**. We are experimenting with it in the Evelyn Partner project (BaaS & ModelBank) as a standalone service enabling the proxy mode. Small configurations are required to include the WireMock root certificate in the Java trust store and release the traffic to the port on Istio.

Further information will be documented soon.