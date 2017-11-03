
## Jigsaw - putting the pieces together

_Samael Bate Senior Developer @ Hermes_

---
### Other Talks/Articles

 - Mark Reinhold (Oracle) "Make Way for Modules!"
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
 - Multi release jars (libraries that work with older JDK while also supporting newer features when being used with JDK 9)
 - Compact Strings (JEP 254 use byte array instead of char array)
 - Javadoc (is now HTML5 with search box)
 - Nashorn improvements (JEP 236: jdk.nashorn.api.tree)
 - GTK3 on Linux (with system property jdk.gtk.version)
 - Platform specific features (JEP 272 interact with taskbar/docks or listen for system/application events)
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

 - encapsulation
 - even if you don't use modules, the fact that the JDK itself is modularised means that functionality that is not intended for use will not be accessible by default. 
 - jlink

---
### Fear, Uncertainty and Doubt

> Oracle is planning to remove sun.misc.Unsafe in Java 9. This is an absolute disaster in the making and has the potential to completely destroy the entire ecosystem around Java.

[dripstat.com](http://blog.dripstat.com/removal-of-sun-misc-unsafe-a-disaster-in-the-making/) 2015

---


### Lets see if a modular JDK breaks Spring


---
### Potential issues

 - JDK structure has changed (rt.jar and tools.jar are gone)
 - Change to version number string. Code that uses `System.getProperty("java.version");` and expects the old style may end up broken
 - Split packages

---?code=JdkVersion.java&lang=java

---


# Lets do some demos