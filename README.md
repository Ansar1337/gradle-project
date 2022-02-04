# Multi-module project on Gradle with step by step instructions

### Description:

We are going to practice creating multi-module projects on Gradle. A standard multi-module project has components by
functionality:

db - database module;

api - web module;

service - a layer of services.

### Implementation:

To get started, create a single module project.

After we create 3 folders in the project directory: db, api, service.

In each of the directories we create build.gradle with this content:

``` xml
plugins {
    id 'java'
}

group 'org.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.0'
}

test {
    useJUnitPlatform()
}
 ```

where we must specify the parent - as the parent module with its data and the name of the artifactId of the current
module
(db, api, service)

To connect new modules to the project, add the newly created modules to the pom.xml file in the project root:

``` xml
<modules>
    <module>db</module>
    <module>api</module>
    <module>service</module>
  </modules> 
 ```

To connect new modules to the project, add the newly created modules to the project root in settings.gradle:

``` xml
include 'db'
include 'service'
include 'api'
 ```

After creating a multi-module project, we will connect related modules to each other.

To connect the db module to the service module, add a dependency:

``` xml
dependencies {
implementation project(":db")
}
 ```

To connect the service module to the api module, add the dependency:

``` xml
dependencies {
implementation project(":db")
implementation project(":service")
}
 ```

Now you can build the project by running the command in the terminal:

``` xml
gradle build
 ```

To demonstrate how modularity works in the db project, let's create classes.

``` java
public class DbSetting {

    private String name;
    private String password;

    public DbSetting(String name, String password) {
        this.name = name;
        this.password = password;
    }
}

public class MyEntity {

    private UUID id;
    private String name;

    public MyEntity(String name) {
        this.name = name;
    }

    public UUID getId() {
        return id;
    }

    public void setId(UUID id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return new StringBuilder().append("MyEntity{").append("id=").append(id).append(", name='").append(name).append('\'').append('}').toString();
    }
}

public class Db {

    private DbSetting dbSetting;
    private MyEntity myEntity;

    public Db(DbSetting dbSetting) {
        this.dbSetting = dbSetting;
        myEntity = new MyEntity("first");
        myEntity.setId(UUID.randomUUID());
    }

    public DbSetting getDbSetting() {
        return dbSetting;
    }

    public MyEntity getMyEntity() {
        return myEntity;
    }

    public void setMyEntity(MyEntity myEntity) {
        this.myEntity = myEntity;
    }
}
 ```

Let's create a class in the Service project:

``` java
public class MyService {

    private DbSetting dbSetting = new DbSetting("name", "password");
    private String name = "myService";
    private Db db = new Db(dbSetting);

    public String getName() {
        return name;
    }

    public MyEntity setMyEntity(MyEntity myEntity) {
        myEntity.setId(UUID.randomUUID());
        db.setMyEntity(myEntity);
        return myEntity;
    }

    public MyEntity getMyEntity() {
        return db.getMyEntity();
    }
}
 ```

Let's create a class in the api project:

``` java
public class Main {

    public static void main(String[] args) {
        MyService myService = new MyService();
        System.out.println(myService.getMyEntity());
        System.out.println(myService.setMyEntity(new MyEntity("second")));
        System.out.println(myService.getMyEntity());
    }
}
 ```

After successfully building the project, we can execute this code.

As a result of this task, we received a modular project with division by functionality
