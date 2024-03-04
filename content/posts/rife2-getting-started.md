---
title: "Getting Started with RIFE2"
date: 2023-01-29T00:00:00-05:00
tags: ["java", "rife2", "gradle", "spring", "web", "beginner"]
summary: "The web has changed significantly since I started my first developer role. So have web frameworks. So have programming languages!"
---

# The Vaporous Nature of Web Development
The web has changed significantly since I started my first developer role. So have web frameworks. So have programming languages!

Despite an **incorrect** reputation of being "old" and "slow", Java has enjoyed a second wind in recent years thanks to a combination of the [Network Effect](https://en.wikipedia.org/wiki/Network_effect) and the regular but stable release cycles. It used to take forever for new Java releases to arrive, but now we can enjoy regular releases every 6 months and Long-Term Support (LTS) releases every 2 years! Java has also gained higher runtime speed, and projects like [GraalVM](https://www.graalvm.org/) and [CRaC](https://www.azul.com/blog/superfast-application-startup-java-on-crac/) are looking to give Java even more speed.

As the Java language evolves, so have the web frameworks. As of the writing of this article, Spring Framework 6 and Spring Boot 3 have recently launched, ushering in the next generation of [Convention Over Configuration](https://en.wikipedia.org/wiki/Convention_over_configuration) development for the Java community.

Spring Boot is my go-to for server-side development, especially for REST API microservices. However, what if you want to prototype something simple and have lower-level control? What if you want to run a simple website over a single Java file with no annotations and no dependency injection?

# Enter RIFE2.
RIFE2 enables quick and simple prototyping, development, and testing without all the bells and whistles. It also ships with a simple two-way, logic-less templating engine.

Best of all, it's *meant* for modern Java syntax!

# Hello World in RIFE2
Given a simple Java + Gradle project layout, running Hello World in RIFE2 is surprisingly little code.

If running a standlone JAR like I am, your `build.gradle` needs to include not just RIFE2, but also Jetty for the underlying server engine and an SLF4J provider for logging. It should look something like this in the dependencies section (but likely with newer version numbers by the time you read this):

```groovy
dependencies {
    implementation 'com.uwyn.rife2:rife2:0.9.5'
    implementation 'org.slf4j:slf4j-simple:2.0.6'
    testImplementation 'org.junit.jupiter:junit-jupiter:5.9.1'

    runtimeOnly 'org.eclipse.jetty:jetty-server:11.0.12'
    runtimeOnly 'org.eclipse.jetty:jetty-servlet:11.0.12'
}
```

And now for the Java code (minus the package name):

```java
import rife.engine.Server;
import rife.engine.Site;

public class App extends Site {
    public void setup() {
        get("/hello", c -> c.print("Hello World"));
    }

    public static void main(String[] args) {
        new Server().start(new App());
    }
}
```

That should be it!!! Now, if you call `gradle run` in the terminal you should be able to see output for something like this:

```
> Task :app:run
[main] INFO org.eclipse.jetty.server.Server - jetty-11.0.12; built: 2022-09-14T02:38:00.723Z; git: d5b8c29485f5f56a14be5f20c2ccce81b93c5555; jvm 17.0.5+9-LTS-191
[main] INFO org.eclipse.jetty.server.session.DefaultSessionIdManager - Session workerName=node0
[main] INFO org.eclipse.jetty.server.handler.ContextHandler - Started o.e.j.s.ServletContextHandler@6fb0d3ed{/,null,AVAILABLE}
[main] INFO org.eclipse.jetty.server.AbstractConnector - Started ServerConnector@64c64813{HTTP/1.1, (http/1.1)}{0.0.0.0:8080}
[main] INFO org.eclipse.jetty.server.Server - Started Server@598067a5{STARTING}[11.0.12,sto=0] @172ms
```

In this case, the server is running on port 8080. Checking the URL `localhost:8080/hello` in the browser should display "Hello World".

# Conclusion
I'm very excited to see what web projects, both big and small, can be accomplished with such a self-contained framework like RIFE2. And we've only scratched the surface! There's a [built-in template system](https://github.com/rife2/rife2/wiki/Bidirectional-Templates), [Continuations](https://github.com/rife2/rife2/wiki/Continuations), and much more. Definitely [read the docs](https://github.com/rife2/rife2/wiki/) if you want to dig deeper into this framework. Also, be sure to thank the framework author, [Geert Bevin](https://twitter.com/gbevin), for the amount of effort he has put into this!