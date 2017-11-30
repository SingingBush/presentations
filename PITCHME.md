
## Jigsaw - putting the pieces together

_Samael Bate Senior Developer @ Hermes_

---
### Other Talks/Articles

 - Mark Reinhold (Oracle) "Make Way for Modules!" and [Modules in One Lesson](https://www.youtube.com/watch?v=rFhhLXcOBsk)
 - Trisha Gee (Jetbrains) "Real World Java 9"
 - Pepper Lebeck-Jobe [Building Java 9 Modules](https://guides.gradle.org/building-java-9-modules/)
 - Jeff Hanson [Modularity in Java 9: Stacking up with Project Jigsaw, Penrose, and OSGi](https://www.javaworld.com/article/2878952/java-platform/modularity-in-java-9.html) 2015 
 - Mike Hearn [Is Jigsaw good or is it wack?](https://blog.plan99.net/is-jigsaw-good-or-is-it-wack-ec634d36dd6f) April 2017
 - Tomer Ben David [Java 9: Step by Step From Zero to Modules](https://dzone.com/articles/java-9-tutorial-step-by-step-from-zero-to-modules) April 2017
 - Simon Ritter [JDK 9: Pitfalls For The Unwary](https://www.azul.com/jdk-9-pitfalls-for-the-unwary/) July 2017
 - Baeldung [Java 9 New Features](http://www.baeldung.com/new-java-9) August 2017

---
### (some) other JDK 9 features

 - Jigsaw (JSR 376modules)
 - Reactive Streams API (JEP266 standard Publisher/Subscriber/Subscription interfaces)
 - Jshell (JEP 222)
 - G1 will be default garbage collector (JEP 248)
 - Various new convenience methods for streams, collections, etc
 - HTTP/2 and websockets (JEP 110 jdk.incubator.http)
 - ...

---
 - Multi release jars (libraries that work with older JDK while also supporting newer features when being used with JDK 9)
 - Compact Strings (JEP 254 use byte array instead of char array)
 - Javadoc (is now HTML5 with search box)
 - Nashorn improvements (JEP 236: jdk.nashorn.api.tree)
 - GTK3 on Linux (with system property jdk.gtk.version)
 - Platform specific features (JEP 272 interact with taskbar/docks or listen for system/application events)
 - ...
---
 - Process API updates (JEP 102)
 - Internationalization (JEP 267 adds Unicode 8.0 support & JEP 226 makes property files UTF-8)
 - Images (javax.imageio.plugins.tiff)
 - Stack Walking (JEP 259)


Check out https://docs.oracle.com/javase/9/whatsnew/toc.htm

---
### Some History

The module system we're getting in Java 9 (JSR 376: Java Platform Module System) has been a long time in the making.

It was originally planned for Java 7.

Prior to JSR 376 there was JSR 277 (June 2005)

---
### Why should we care?

 - encapsulation (modules prevent access via reflection, unless *opens* is used in module-info)
 - even if you don't use modules, the fact that the JDK itself is modularised means that functionality that is not intended for use will not be accessible by default. 
 - jlink (more on this later)

---
### Fear, Uncertainty and Doubt

> Oracle is planning to remove sun.misc.Unsafe in Java 9. This is an absolute disaster in the making and has the potential to completely destroy the entire ecosystem around Java.

[dripstat.com](http://blog.dripstat.com/removal-of-sun-misc-unsafe-a-disaster-in-the-making/) 2015

---
### Potential issues

 - JDK structure has changed (rt.jar and tools.jar are gone)
 - API's for things like JAXB (xml binding) and JAX-WS (web service) aren't on classpath by default
 - Change to version number string. Code that uses `System.getProperty("java.version");` and expects the old style may end up broken
 - When using modules Java 9 does not allow split packages (identical packages in multiple modules)

---?code=JdkVersion.java&lang=java

---
### Why am I getting ClassNotFoundException?

In JDK 9, the modules that contain CORBA or the APIs shared between Java SE and Java EE are not resolved by default when you compile or run code on the class path. These are...

---
- *java.corba* — CORBA
- *java.transaction* — The subset of the Java Transaction API defined by Java SE
- *java.activation* — JavaBeans Activation Framework
- *java.xml.bind* — Java Architecture for XML Binding (JAXB)
- *java.xml.ws* — Java API for XML Web Services (JAX-WS)
- *java.xml.ws.annotation* — subset of the JSR-250 Common Annotations to support web services
---

using the *java.se.ee* aggregate module as a temporary workaround would add all the modules on previous slide (it's not recommended though).


They are marked as `@Deprecated(forRemoval=true)` and could be gone by the next major release (next year).

---
### potential workarounds

 - `--add-modules java.xml.bind,java.transaction` can be used to packages back on the classpath
 - If you still want to compile on JDK 8, use `-XX:+IgnoreUnrecognizedVMOptions` along with newer args
 - loads more, read the docs

These should only be used as temporary workarounds, moving forward we should make modules.

---


### Lets see if the modular JDK breaks Spring

---
### Spring

 - v4: depending on what you're using will require various amounts of effort 
 - v5: is Java 9 (both module- and classpaths) ready out of the box

---
### add a module-info.java

```
module com.domain.service {
    requires com.domain.service;

    requires transitive some.other.thing;

    exports com.domain.stuff.service;

    provides com.domain.service.EventService with com.domain.stuff.service.StuffService;
}
```

---
### automatic modules

slf4j-api-1.7.25.jar becomes slf4j.api

---

### Lets do some code

---
### Java has a new class

`java.lang.Module` which means we can do `this.getClass().getModule()`

---
### Maven

 - Standard maven plugins are generally ok but anything else you could be stuck
 - use Maven >= 3.5.0
 - maven-jar-plugin >= 3.0.2
 - maven-compile-plugin >= 3.7.0
 - in the _spring-boot-maven-plugin_ use `<jvmArguments>--add-modules java.xml.bind --illegal-access=permit</jvmArguments>`

---
### Gradle

 - Standard gradle plugins are generally ok but anything else you could be stuck
 - gradle users may have it easier due to the way that a build file is Groovy (or Kotlin) so it's easier to do custom steps
 - Chainsaw plugin (fork of gradle-java-modules plugin) shows a lot of promise

---
### build.gradle

```
test {
    inputs.property("moduleName", moduleName)
    doFirst {
        jvmArgs = [
            '--module-path', classpath.asPath, 
            '--add-modules', 'ALL-MODULE-PATH', 
            '--add-reads', "$moduleName=junit", 
            '--patch-module', "$moduleName=" + files(sourceSets.test.java.outputDir).asPath, 
        ]
        classpath = files()
    }
}
```

---


### opinions anyone?
