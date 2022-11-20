**flyway는 데이터베이스의 형상관리를 목적으로 하는 툴**입니다. 데이터베이스의 형상 관리란 어떤 것일까요? git을 통하여 우리가 코드를 관리하는 것의 데이터베이스 버전으로 볼 수 있습니다. git에서는 코드를 파일별로 로깅을 통해서 변화의 이력을 추적합니다. **flyway는 데이터베이스의 DDL의 이력을 쌓아서 DDL이 어떻게 변화되었는지 관리하는 툴**로 사용할 수 있습니다.

It is based around just 7 basic commands: [Migrate](https://flywaydb.org/documentation/command/migrate), [Clean](https://flywaydb.org/documentation/command/clean), [Info](https://flywaydb.org/documentation/command/info), [Validate](https://flywaydb.org/documentation/command/validate), [Undo](https://flywaydb.org/documentation/command/undo), [Baseline](https://flywaydb.org/documentation/command/baseline) and [Repair](https://flywaydb.org/documentation/command/repair).

https://flywaydb.org/documentation/

# Why database migrations?

First, let's start from the beginning and assume we have a project called _Shiny_ and its primary deliverable is a piece of software called _Shiny Soft_ that connects to a database called _Shiny DB_.

The simplest diagram to represent this could look something like this:

![](https://flywaydb.org/assets/balsamiq/SimpleView.png)

We have our software and our database. Great. And this could very well be all you need.

But on most projects, this simple view of the world very quickly translates into this:

![](https://flywaydb.org/assets/balsamiq/Environments.png)

We now not only have to deal with one copy of our environment, but with several. This presents a number of challenges.

**We have been pretty good at solving them on the code side.**

-   Version control is now universal with better tools everyday.
-   We have reproducible builds and continuous integration.
-   We have well defined release and deployment processes.

![](https://flywaydb.org/assets/balsamiq/SoftGreen.png)

**But what about the database?**

![](https://flywaydb.org/assets/balsamiq/DbRed.png)

Well unfortunately we have not been doing so well there. Many projects still rely on manually applied sql scripts. And sometimes not even that (a quick sql statement here or there to fix a problem). And soon many questions arise:

-   What state is the database in on this machine?
-   Has this script already been applied or not?
-   Has the quick fix in production been applied in test afterwards?
-   How do you set up a new database instance?

More often than not the answer to these questions is: We don't know.

**Database migrations are a great way to regain control of this mess.**

They allow you to:

-   Recreate a database from scratch
-   Make it clear at all times what state a database is in
-   Migrate in a deterministic way from your current version of the database to a newer one


# How Flyway works

The easiest scenario is when you point **Flyway** to an **empty database**.

![](https://flywaydb.org/assets/balsamiq/EmptyDb.png)

It will try to locate its **schema history table**. As the database is empty, Flyway won't find it and will **create** it instead.  You now have a database with a single empty table called _flyway_schema_history_ by default:

![](https://flywaydb.org/assets/balsamiq/EmptySchemaVersion.png)

This table will be used to track the state of the database.

Immediately afterwards Flyway will begin **scanning** the filesystem or the classpath of the application for **migrations**. They can be written in either Sql or Java.

The migrations are then **sorted** based on their **version number** and applied in order:

![](https://flywaydb.org/assets/balsamiq/Migration-1-2.png)

As each migration gets applied, the schema history table is updated accordingly:


With the metadata and the initial state in place, we can now talk about **migrating to newer versions**.

Flyway will once again scan the filesystem or the classpath of the application for migrations. The migrations are checked against the schema history table. If their version number is lower or equal to the one of the version marked as current, they are ignored.  
  
The remaining migrations are the **pending migrations**: available, but not applied.

![](https://flywaydb.org/assets/balsamiq/PendingMigration.png)

They are then **sorted by version number** and **executed in order**:

![](https://flywaydb.org/assets/balsamiq/Migration21.png)

The **schema history table** is **updated** accordingly:

**And that's it!** Every time the need to evolve the database arises, whether structure (DDL) or reference data (DML), simply create a new migration with a version number higher than the current one. The next time Flyway starts, it will find it and upgrade the database accordingly.

[First steps](https://flywaydb.org/documentation/getstarted/firststeps/commandline)