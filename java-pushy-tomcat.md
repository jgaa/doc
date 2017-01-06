# Apple Push Notifications from a Tomcat Container

I had to make this work for a project. It was not trivial.

The APN Java Class connects to Apple's push API services over HTTP/2. This causes some challenges, because HTTP/2 is not yet supported by Java. The core of the problem is that HTTP/2 require Application-Layer Protocol Negotiation (ALPN) within TLS. In Java, the TLS support is provided by native code, wrapped in Java classes. In order to support HTTP/2, we need to use libraries that overrides the supplied Socket/TLS libraries in Java (using the namespaces(s) in the Java SDK).

To talk about Push notifications over HTTP/2 with Apple, we use a Java library called “[Pushy](https://github.com/relayrides/pushy)”. It’s easy to use, but it’s not designed to be used inside a Tomcat container. Running inside Tomcat, it bumps into Tomcat’s own constraints regarding HTTP/2.

Tomcat has a native library to speed things up (It’s interesting to observe that whenever someone need really high performance in Java - they tend to end up with native code, wrapped in a Java interface). For HTTP/2 to work, we need to use a fork of Tomcat’s native library that implement the missing pieces (in Tomcats name-space).

The documentation about how to do all this is scattered around in wiki’s, github tickets, and project pages. Most of the information is incomplete, obsolete or incorrect. So I have tried a large number of combinations of different alternatives in order to make it work.

For the [Java Connection/TLS override](http://www.eclipse.org/jetty/documentation/9.4.x/alpn-chapter.html), we need a version that matches the version of Java that is used by Tomcat. Note that this project tracks changes on OpenJDK, not official Oracle Java. However, my tests with Oracle Java has shown that this works as well (provided that we use the correct version of the library).

For the [Tomcat native fork](https://github.com/netty/netty-tcnative), I recommend to use the latest stable release. Note that this is native code wrapped around OpenSSL (a snake's nest of yet undisclosed vulnerabilities). You can browse the release (timeline) history along with the fixed issues [here](https://github.com/netty/netty-tcnative/milestones?state=closed).

In my lab, I use Tomcat with Oracle Java 1.8.0_51-b16. In this setup, with the latest Project .war file, the only required setting is to add “-Xbootclasspath/p:/opt/tomcat/lib/[alpn-boot-8.1.4.v20150727.jar](http://central.maven.org/maven2/org/mortbay/jetty/alpn/alpn-boot/8.1.4.v20150727/alpn-boot-8.1.4.v20150727.jar)" to JAVA_OPTS in /opt/tomcat/bin/setenv.sh. (I would have preferred a https download link for the library, but the maven guys have not configured their web servers / certificates correctly).

Note that the projects .pom file includes:
```xml
    <dependency>
        <groupId>com.relayrides</groupId>
        <artifactId>pushy</artifactId>
        <version>0.8.1</version>
    </dependency>
    <dependency>
        <groupId>io.netty</groupId>
        <artifactId>netty-tcnative-boringssl-static</artifactId>
        <version>1.1.33.Fork25</version>
    </dependency>
    <dependency>
        <groupId>org.mortbay.jetty.alpn</groupId>
        <artifactId>alpn-boot</artifactId>
        <!-- NB: The version must match the Java version. Ref: http://www.eclipse.org/jetty/documentation/current/alpn-chapter.html
            8.1.4.v20150727 is for 1.8.0u51
        -->
        <version>8.1.4.v20150727</version>
        <scope>runtime</scope>
    </dependency>
```

So, in order to make the APN Java Class work, make sure you use Oracle Java 1.8.0_51-b16, download the alpn-boot-8.1.4.v20150727.jar library, and point to it’s location in Tomcat’s setenv.sh file. If you use another Java version, find the correct [version](http://www.eclipse.org/jetty/documentation/9.4.x/alpn-chapter.html#alpn-versions) of the alpn-boot library, download it from [maven central](https://mvnrepository.com/artifact/org.mortbay.jetty.alpn/alpn-boot), and configure setenv.sh accordingly.

Good Luck! (You'll need it).
