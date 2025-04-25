Sample project created via `gradle init --type java-application --dsl groovy`, then modified
to produce a java annotation processor and a library to test with publishing and simplified.

Most of the sample code and modules have been removed, to most easily demonstrate the issue, 
without potentially clouding the problem with `maven-publish` or other plugins.

In short, utilities/build.gradle uses an annotation processor, and enables creation of a source
jar:
```groovy
java.withSourcesJar()

dependencies {
    // Sample annotation processor, any one will result in this issue
    implementation "com.google.dagger:dagger:2.56.2"
    annotationProcessor "com.google.dagger:dagger-compiler:2.56.2"
}
```

When the sourcejar is built, via `./gradlew assemble`, the generated source jar is missing the DaggerCoffeeComponent type:
```shell
$  unzip -l utilities/build/libs/utilities-sources.jar
Archive:  utilities/build/libs/utilities-sources.jar
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2025-04-25 13:35   META-INF/
       25  2025-04-25 13:35   META-INF/MANIFEST.MF
        0  2025-04-18 12:24   org/
        0  2025-04-18 12:24   org/example/
        0  2025-04-18 12:24   org/example/utilities/
      138  2025-04-18 12:24   org/example/utilities/CoffeeComponent.java
       97  2025-04-18 12:24   org/example/utilities/HeaterModule.java
---------                     -------
      260                     7 files
```

When the workaround in utilities/build.gradle is enabled and the project rebuilt, the sources jar is complete:
```shell
$ unzip -l utilities/build/libs/utilities-sources.jar
Archive:  utilities/build/libs/utilities-sources.jar
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2025-04-25 13:37   META-INF/
       25  2025-04-25 13:35   META-INF/MANIFEST.MF
        0  2025-04-18 12:24   org/
        0  2025-04-18 12:24   org/example/
        0  2025-04-18 12:24   org/example/utilities/
      138  2025-04-18 12:24   org/example/utilities/CoffeeComponent.java
       97  2025-04-18 12:24   org/example/utilities/HeaterModule.java
     1008  2025-04-25 13:35   org/example/utilities/DaggerCoffeeComponent.java
---------                     -------
     1268                     8 files
```
