# Token Decorating Route Service

This project is an example of a [Cloud Foundry Route Service][r] written with [Spring Boot][b].  This application does the following to each request:

1. Intercepts an incoming request
2. Logs information about that incoming request
3. Transforms the incoming request to an outgoing request
4. Adds a header with a vault token for the config server to use
4. Logs information about that outgoing request
5. Forwards the request and response

Item #4 is the key to the solution; it means that the individual client apps that use the config server that this service is bound in front of need not have the vault token in their environment or bootstrap.properties.

## Requirements
### Java, Maven
The application is written in Java 8 and packaged as a self executable JAR file. This enables it to run anywhere that Java is available.

## Deployment
_The following instructions assume that you have [created an account][c] and [installed the `cf` command line tool][i]._

In order to automate the deployment process as much as possible, the project contains a Cloud Foundry [manifest][y].  To deploy run the following commands:
```bash
$ ./mvnw clean package
$ cf push
```

Next, create a user provided service that contains the route service configuration information.  To do this, run the following command, substituting the address that the route service is listening on:
```bash
$ cf create-user-provided-service config-server-route-service -r https://<ROUTE-SERVICE-ADDRESS>
```

The next step assumes that you have a config server already running that you'd like to bind this route service to.  To do this, run the following command, substituting the domain and hostname bound to that config server:
```bash
$ cf bind-route-service <APPLICATION-DOMAIN> config-server-route-service --hostname <APPLICATION-HOST>
```

In order to view the interception and decoration of the requests, you will need to stream the logs of the route service.  To do this, run the following command:
```bash
$ cf logs token-decorator
```



## License
The project is released under version 2.0 of the [Apache License][a].


[a]: http://www.apache.org/licenses/LICENSE-2.0
[b]: http://spring.io/projects/spring-boot/
[c]: https://console.run.pivotal.io/register
[i]: http://docs.run.pivotal.io/devguide/installcf/install-go-cli.html
[j]: http://www.jetbrains.com/idea/
[r]: http://docs.cloudfoundry.org/services/route-services.html
[y]: manifest.yml
