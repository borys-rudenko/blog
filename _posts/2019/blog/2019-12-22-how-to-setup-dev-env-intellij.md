---
title:  "Efficient java dev environment setup with IntelliJ IDEA"
excerpt: In this article I am going to show you how to setup java environment using IntelliJ IDEA for efficient development
date:   2019-12-22 00:00:00 +0100
categories: blog
toc: true
toc_label: On this pages
toc_sticky: true

header:
  teaser: assets/images/2019/blog/how-to-setup-dev-env-intellij/how-to-setup-dev-env-intellij-teaser.jpg
  overlay_image: assets/images/2019/blog/how-to-setup-dev-env-intellij/how-to-setup-dev-env-intellij.png
---

# Intro
Hey there. In this article I am gonna show you several options on how you can set up one of the most popular IDE for java development IntelliJ IDEA for quick and efficient development based on my own experience.

In the scope of this article I am not going to talk about the code creation/refactoring features provided by IntelliJ. We are going to talk mainly about an initial project setup plus tooling which we can use out of the box. Moreover we are going to create a simple CRUD web application from scratch as an example.

Are you new to IntelliJ? - You may find this article really useful. Are you a mature user? - Check it out anyway. I am pretty sure you may pick up a couple of new features as well.

So, let's get started.

# Test Project creation
In order to demonstrate everything graphically we need to have an example project. Probably even two.
I've decided to build them from scratch in order to make everything completely clear. And you can easily repeat the same by yourself later.
In this demo I've chosen **maven** as a build tool, but everything from this article could be done in a similar way with **gradle** as well.

There are several options on how to create a java project from scratch.

The best and simplest way in most cases would be to just open [Spring Initializr](http://start.spring.io/), add the groupId/artifactId and libs based on your project needs(Spring Web, JPA, H2) and click Download. We are done. This will be a **_project1-springboot_** in our test exercise.

But in the real-world there are still a lot of apps based on the monolith design (which is completely fine). Usually, they are built as a **multi-module maven** project.
So, let's generate it as well. We can do it with maven archetypes. The name will be **_project2-multimaven_**

This command will create a parent maven project for us in the non-interactive mode

```bash
mvn archetype:generate \
-DarchetypeGroupId=org.codehaus.mojo.archetypes \
-DarchetypeArtifactId=pom-root \
-DarchetypeVersion=RELEASE \
-DgroupId=com.brudenko \
-DartifactId=project2-multimaven \
-Dversion=0.0.1-SNAPSHOT \
-DinteractiveMode=false
```

And now we can generate 3 submodules. Just make a step inside parent maven project and run the next command 3 times.
Let's name them _controller_, _service_, _dao_. But it could be anything of course.

```bash
mvn archetype:generate \
-DarchetypeGroupId=org.apache.maven.archetypes \
-DarchetypeArtifactId=maven-archetype-quickstart \
-DarchetypeVersion=RELEASE \
-DgroupId=com.brudenko \
-DartifactId=dao \
-Dversion=0.0.1-SNAPSHOT \
-DinteractiveMode=false
```

So now we have 2 projects: **_project1-springboot_** and **_project2-multimaven_**


At the beginning when we generated **_project1-springboot_** we chose the next dependencies: Spring Web, JPA, H2. After project generation we can see all of them in our **pom.xml** file:
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>
  <dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

Now, let's create a super simple Web application, which will print the name of all or specific users and can persist these users into DB.
In order to do this, we need to create the next classes:

```java
@Data
@Entity
@Table(name = "user")
public class UserEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;
    private String name;
}

@Repository
interface UserRepository extends CrudRepository<UserEntity, Long> {}

@RestController
public class UserController {
    @Autowired
    private UserRepository userRepository;

    @GetMapping("/user")
    public Iterable<UserEntity> getAllUsers() {
        return userRepository.findAll();
    }

    @GetMapping("/user/{id}")
    public ResponseEntity<UserEntity> getUserById(@PathVariable(value="id") Long userId) {
        UserEntity user = userRepository.findById(userId)
            .orElseThrow(() -> new EntityNotFoundException("User not found with id " + userId));
        return ResponseEntity.ok().body(user);
    }

    @PostMapping("/user")
    public UserEntity createUser(@RequestBody UserEntity userEntity) {
        return userRepository.save(userEntity);
    }
}
```

Where **UserEntity** is our domain object (data will be persisted in the table _USER_)

**UserRepository** is a spring data repository, responsible for persisting/fetching of entities from the DB.

**UserController** is responsible for processing incoming requests. In our case the URL is _GET/POST /user_ and _GET /user/{id}_

We are going to use this app for demonstration of the IDE feature later in this article.

But now let's see how we can organize our workspace.

# Core Tools

## Multiproject workspace
_**Multiproject workspace**_ - that's one of my favourite features in IntelliJ IDEA.

Not everyone knows it, but IntelliJ IDEA gives you a great possibility to open as many projects as you want in a single instance(window).

It could be super useful when working with several different repos at the same time. So, in this case it's not necessary to open several windows.
Plus, the whole tool supports it perfectly and I am going to show you this a bit later.

It's also possible to have a multi-project workspace if the projects have different nature (BE/FE stack) or built with different tools, like maven/gradle etc.

Of course I am not suggesting adding all your projects to the same workspace. But if you are constantly working with several related repos it will be very handy to have all of them in a single place (multiproject).

> Multiproject workspace is an extremely nice and important feature, which I strongly suggest you trying

## Maven integration

So, let's open the **_project2-multimaven_** project in the IDE. In order to do this just open IDE and go to **_File -> Open_** and select the **pom.xml** file of the project.

So now in the Maven toolbar you can see all the submodules from our project.

![Maven project structure](/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_1_maven_project_1.png)


Let's add the second **_project1-springboot_** into the same workspace. So in the maven plugin we just click **+** and select a **pom.xml** file from the project.
Now in the **_Project_** toolbar we can see our 2 projects and also in the _Maven_ toolbar we can see all maven modules from all subprojects.

![Maven project 2 structure](/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_2_maven_project_2.png)

As you can see right now it's not really informative, because all our projects and modules are just listed without any grouping or structure making, so it's really hard to find and build the correct one and understand to which project it belongs.

So, let's improve it immediately. If you click the **Settings** icon in the **_Maven_** plugin and click **Group Modules** - our projects and modules will be regrouped:

![Maven project groupped](/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_3_maven_projects_groupped.png)

Much better, because now all maven modules are grouped in a logical hierarchy. First by project and after that by submodules.

Now if we want to do a full build of one of the projects or of the specific submodule - we just need to open **Lifecycle** package and run the required maven task or several at once.
If you have any maven **Profiles** in your app - it will also be displayed in the same plugin in the **Profile** package, so you can visually choose everything you want.
Same with maven plugins. There is a **Plugin** package, so you can execute them from there.

Let's add one simple profile into our **_project1-springboot_**  **pom.xml** file:

```xml
<profiles>
  <!-- skip unit test -->
  <profile>
    <id>skip-test</id>
    <properties>
      <maven.test.skip>true</maven.test.skip>
    </properties>
  </profile>
</profiles>
```
Now we can run build of the project using terminal and **-P** flag:

```bash
$ mvn package -Pskip-test
```
Or we can simply activate/deactivate it with _**Maven**_ plugin, because now we can see it on the list of **Profiles**:

![Maven project profiles](/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_4_maven_projects_profiles.png)

So, you can use this toolbar for the quick execution of your build tasks. You can do exactly the same manually using the terminal of course, so it's up to you what to choose. I am going to show you how to execute build with the embedded [terminal](#terminal) later on in this article.

## Database integration (DataGrip)

In IntelliJ IDEA we have a great DB plugin (DataGrip) which has quite a lot of useful features like in any specialized SQL editor.
And it can work with different databases at the same time and in the same window without installing any additional software. Sounds good? Let's check it on example.

Let's run our app and add several users into the DB using our WEB app. (You can check how to do it in the [HTTP Client](#http-client) paragraph of this article)

I will put DB settings here in order to demonstrate our H2 configuration:
```properties
spring.datasource.url= jdbc:h2:~/testdb;AUTO_SERVER=TRUE
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
```
We are using H2 in the **embedded** mode, because there is no way how to connect to the DB in the **in-memory** mode.
So, it will be persisted into a file and we can easily make a separate connection to it.

Let's imagine that it's a complex type of data and we want to manually check the DB and analyze it. Normally in order to do so we have to install a separate tool, like Mysql Workbench / Oracle SQL developer or any other universal SQL editor.

But we can do it with **Database** IntelliJ plugin. For this just open the plugin and select **_New -> Data source -> H2_** and specify connection details as we have in our properties file.

<figure class="half">
    <a href="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_5_db_connection.png">
    <img src="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_5_db_connection.png">
    </a>
    <figcaption>H2 DB embedded connection example(click to zoom)</figcaption>
</figure>

Now we can see the full structure of our tables and we can do different operations with it, like modifying the structure, generating SQL scripts for data, import/export data and many other things.
I'll show you several most useful features. Let's click on the _USER_ table, to be able to see our table data.

Using this UI you can easily add another row, modify or delete an existing one and even add some query params to the filter(please check the screenshot below).

If you want to execute something manually - open a separate _**SQL console**_ from **Database** plugin and execute any query.

<figure class="half">
    <a href="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_6_db_editor_1.png">
    <img src="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_6_db_editor_1.png">
    </a>
    <a href="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_7_db_editor_2.png">
    <img src="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_7_db_editor_2.png">
    </a>
    <figcaption>DB SQL editor(click to zoom)</figcaption>
</figure>

As you can see you can easily execute any SQL scripts, navigate through the tables data and modify the structure. There are a lot of other not so important features which I left out of scope, but I think it's more than enough for a regular developer.

> You can add as many DB connections to the different databases as you want and check all of them in the Database IntelliJ plugin.

## VCS integration (GIT)
Another super powerful feature - integration with main VCS systems, like _git_, _mercurial_, etc.
In my personal opinion GUI tools are much more convenient than using terminal for this. Because it gives you a perfect visibility of everything going on and shooting yourself in the foot becomes a much harder deal.

Note. Of course, if you are a beginner and just learning any VCS tool - it's good to start with the terminal trying to get basic understanding.
But if you are an experienced user, in most cases it's overhead and it's almost like using Notepad instead of IDE :)
{: .notice}

So built-in IntelliJ VCS tool is a great alternative to the specialized tools like: _SourceTree/GitKraken/Tortoise HG_ and many others.

Let's just play a bit with our projects and check the main VCS integration features.
First , we have **Git branches** plugin. It's located in right bottom corner of the IDE and you can see the current branch there. Seems we have a multi-project setup, the branch name will change automatically based on an open file.
Clicking on the branch name you will see a menu, where you can find all your projects and current branches. From this menu you can also create a new branch or checkout to any existing one. Here is a short illustration:

![VCS branching](/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_8_vcs_branches.png)

The next super useful thing would be the default **Version control** plugin. If you click on it you can see all uncommitted local changes, as well as commits and information about all your projects. You can filter commits by repo/date/user/name or any other criteria. If you want, you can do cherry-pick/patches/revert/merge/rebase/etc of commits from here as well. And of course, you can easily see and navigate all particular commit changes.

Git is integrated into the **Context menu** as well. So in any file just right click to find **_Git_** submenu, which gives you a possibility to view the commit history of this file or compare it to the same file from another branch. Or you can just do _git pull_ from here and this will execute git pull for you only for repo (which this file belongs to)

<figure>
    <a href="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_9_vcs_plugin.png">
    <img src="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_9_vcs_plugin.png">
    </a>
    <figcaption>OOTB VCS Plugin (click to zoom)</figcaption>
</figure>

And the last feature for this article. Right-click in the left part of the code area and select **Annotate** .This will open a full history of the file, and you will be able to quickly identify when any change has been done. Just click any line
in the Annotate section and you will see all changes from that commit:


<figure>
    <a href="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_10_vcs_annotate.png">
    <img src="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_10_vcs_annotate.png">
    </a>
    <figcaption>VCS Annotation feature(click to zoom)</figcaption>
</figure>


That's just a brief overview of the core features. You can find even more, and they keep improving it in each release.

> As for me integration is super powerful, especially the way it works with the multi-project environment is just awesome.

# Additional tools

## Terminal
In IntelliJ we have an embedded terminal emulator. It's very similar to a system terminal and provides similar functionality.
From useful features, you can open several independent sessions(tabs). Also, you can drag and drop the folder from the project to setup a path.

So, if you have something very specific or you just want to use maven/vcs without GUI - you can do the same just typing the command manually in the same IDE window.

You can execute any command you want even not related to the project. The benefit here is that you don't need to switch between applications.

![Terminal](/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_11_terminal.png)

## Http client

There is a preinstalled tool in IntelliJ called **HTTP Client**. So, it's like a simple alternative to tools like _Postman_ or _Insomnia_.
For simple projects it could be quite useful. It provides the ability to compose and execute HTTP requests from the code editor.
I won't go into all features, because there are a lot and it keeps improving.

Let's try to call our API from our test **_project1-springboot_** application.
Our goal is the following:

>Create 3 users with names **Borys**, **Josh** and **Mark** using our **POST /user** API and after that call **GET /users** in order to see all of them.

So, let's start our **_Project1SpringbootApplication_** and go to the **Tools** menu and select _**HTTP Client -> Test RESTful web service**_.
We can specify all parameters, headers, cookies, request body, etc and execute our query.

<figure class="half">
    <a href="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_14_rest_client_1.png">
    <img src="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_14_rest_client_1.png">
    </a>
    <a href="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_15_rest_client_2.png">
    <img src="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_15_rest_client_2.png">
    </a>
    <figcaption>HTTP Client demo(click to zoom)</figcaption>
</figure>

If you were attentive you can see in the screenshot that **HTTP Client** GUI tool is deprecated now. And IDE suggests transforming requests to a new format.
This is under development right now in my current version of my IDE (2018.2.8), so I expect some changes in the nearest future.
But the main idea is, that now we can store our requests in a separate **.http** file. It can contain many requests inside and we can execute it as a collection. Also, as an output it will generate a **.json** file with responses for us.
So, we can keep our request collections under source control and easily recheck our functionality. We can also extract some environment variables into separate files, so we can reuse our **.http** files with different configurations. It looks very similar to Postman.

<figure class="half">
    <a href="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_16_rest_client_3.png">
    <img src="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_16_rest_client_3.png">
    </a>
    <figcaption>New format of HTTP Client(click to zoom)</figcaption>
</figure>

Looking forward for the upcoming features inside HTTP Client in the next releases and I hope one day it becomes as powerful as Postman.

## SSH tool
You can launch an SSH Session right from IntelliJ IDEA.
In order to setup a new connection go to  _**Tools** -> **Deployment** -> **Configuration**_.
Add a new server with the **+** button. Enter the name and select a type.
Now you can enter _host_, _username_ and _password_ (or key pair).
Next time you select _**Tools** -> **Start SSH session**_ there should be your server name. And you can create several different connections.

![SSH Tool](/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_12_ssh_tool.png)

So, if you are working with SSH you can specify all connection settings inside the IDE and have a quick access to them.

## Plugins

Another super powerful feature is a plugins system. In the **Plugins** menu you can install additionally a lot of specific extensions provided by different companies.
So just for example there are _markdown/uml integration/docker/jira/chekstyle_ plugins and many others.

Here you can decide by yourself which features and technologies you want to base your project on.

<figure>
    <a href="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_13_plugins.png">
    <img src="/assets/images/2019/blog/how-to-setup-dev-env-intellij/screen_13_plugins.png">
    </a>
    <figcaption>Docker plugin installation example(click to zoom)</figcaption>
</figure>

> Note. You can always extend the OOTB IDE functionality with various plugins based on your tech stack.


# Summary
You need to keep in mind, that usually embedded tools are not as powerful as a separate single purpose application of course. But at the same time in most cases it's completely enough. And you can always install both, compare and choose the one which will make your life easier in the long run.

Let's shortly summarize the experience of using Intellij IDEA described in this article.

**Advantages:**
- Easy all-in-one setup. Install 1 app - got a lot of features out of the box.
- It’s a cross-platform, so the same experience with any OS and no pain with extra tools setup.
- Multi-project support. You can manage several different repos (projects) in the same window.
- Powerful VCS integration.
- Embedded DB editor. Supports many connections.
- Perfect build tools integration (maven/gradle/etc)
- Easy functionality extension with plugins

**Disadvantages:**
 - IntelliJ IDEA is not free. There is a _Community edition_, but it doesn't have all of its power. I think for beginners it could be a good choice (if you are just learning). But if you a professional developer it should not be a problem to buy/get a license, I hope.
 - It's quite heavy and requires some hardware resources, so probably it won't be extremely fast on the slow PCs.


PS: There are several free alternative IDE for java development like Eclipse or NetBeans. But personally, I am not big fan of them. I tried several times only too find out that these IDE are not comfortable for me, making my efficiency much lower with them. If you have a better experience, please let me know if there any possibility to do something similar using Eclipse or any other Java IDE.
{: .notice}


So, it’s up to you what to choose, I just gave you a generic overview of what I have been using for a long time on a daily basis and how to use it efficiently.

If you want to discuss it deeply just let me know or if you have any questions or something to add you are very welcome to leave a comment.
