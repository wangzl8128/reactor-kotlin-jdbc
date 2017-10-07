# RxKotlin-JDBC

Fluent, concise, and easy-to-use extension functions targeting JDBC in the Kotlin language with [RxJava 2.0](https://github.com/ReactiveX/RxJava).

This library is inspired by Dave Moten's [RxJava-JDBC](https://github.com/davidmoten/rxjava-jdbc) but seeks to be much more lightweight by leveraging Kotlin functions. This also works best with a threadpool `DataSource` library such as [HikariCP](https://github.com/brettwooldridge/HikariCP). 


## Binaries

**Maven**

```xml
<dependency>
  <groupId>org.nield</groupId>
  <artifactId>rxkotlin-jdbc</artifactId>
  <version>0.0.1</version>
</dependency>
```

**Gradle**

```groovy
repositories {
    mavenCentral()
}
dependencies {
    compile 'org.nield:rxkotlin-jdbc:0.0.1'
}
```


You can also use JitPack.io to build a snapshot with Maven or Gradle:

**Gradle**

```groovy
repositories {
    maven { url 'https://jitpack.io' }
}
dependencies {
    compile 'com.github.thomasnield:rxkotlin-jdbc:-SNAPSHOT'
}
```

**Maven**

```xml
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>

<dependency>
    <groupId>com.github.thomasnield</groupId>
    <artifactId>rxkotlin-jdbc</artifactId>
    <version>-SNAPSHOT</version>
</dependency>
```

## Usage Example: 

```kotlin
val config = HikariConfig()
config.jdbcUrl = "jdbc:sqlite:/home/thomas/Desktop/test.db"
config.minimumIdle = 3
config.maximumPoolSize = 10

val ds = HikariDataSource(config)

with(ds) {
    execute("CREATE TABLE USER (ID INTEGER PRIMARY KEY, USERNAME VARCHAR(30) NOT NULL, PASSWORD VARCHAR(30) NOT NULL)")
    execute("INSERT INTO USER (USERNAME,PASSWORD) VALUES (?,?)", "thomasnield", "password123")
    execute("INSERT INTO USER (USERNAME,PASSWORD) VALUES (?,?)", "bobmarshal","batman43")
}

ds.select("SELECT * FROM USER)
        .toObservable { it.getInt("ID") to it.getString("USERNAME") }
        .subscribe(::println)


ds.select("SELECT * FROM USER WHERE ID = ?", 2))
        .toSingle { it.getInt("ID") to it.getString("USERNAME") }
        .subscribeBy(::println)
```

**OUTPUT:**

```
(1, thomasnield)
(2, bobmarshal)
```
