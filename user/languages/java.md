---
title: Building a Java project
layout: en

---

### What This Guide Covers

This guide covers build environment and configuration topics specific to Java projects. Please make sure to read our [Getting Started](/user/getting-started/) and [general build configuration](/user/customizing-the-build/) guides first.

<div id="toc"></div>

## Overview

The Travis CI environment provides recent versions of Oracle JDK 8 (default), Oracle JDK 9, OpenJDK 7, Gradle 2.0, Maven 3.2 and Ant 1.8, and has sensible defaults for projects that use Gradle, Maven or Ant.

To use the Java environment add the following to your `.travis.yml`:

```yaml
language: java
```
{: data-file=".travis.yml"}

Older OpenJDK and Oracle JDK versions may be available on [Precise](/user/reference/precise#JDK).

## Projects Using Maven

### Default script Command

If your project has `pom.xml` file in the repository root but no `build.gradle`, Travis CI builds your project with Maven 3:

```bash
mvn test -B
```

If your project also includes the `mvnw` wrapper script in the repository root, Travis CI uses that instead:

```bash
./mvnw test -B
```

> The default command does not generate JavaDoc (`-Dmaven.javadoc.skip=true`).

To use a different build command, customize the [build step](/user/customizing-the-build/#Customizing-the-Build-Step).

### Dependency Management

Before running the build, Travis CI installs dependencies:

```bash
mvn install -DskipTests=true -Dmaven.javadoc.skip=true -B -V
```

or if your project uses the `mvnw` wrapper script:

```bash
./mvnw install -DskipTests=true -Dmaven.javadoc.skip=true -B -V
```

## Projects Using Gradle

### Default script Command

If your project has `build.gradle` file in the repository root, Travis CI builds your project with Gradle:

```bash
gradle check
```

If your project also includes the `gradlew` wrapper script in the repository root, Travis CI uses that instead:

```bash
./gradlew check
```

To use a different build command, customize the [build step](/user/customizing-the-build/#Customizing-the-Build-Step).

### Dependency Management

Before running the build, Travis CI installs dependencies:

```bash
gradle assemble
```

or

```bash
./gradlew assemble
```

### Caching

A peculiarity of dependency caching in Gradle means that to avoid uploading the cache after every build you need to add the following lines to your `.travis.yml`:

```yaml
before_cache:
  - rm -f  $HOME/.gradle/caches/modules-2/modules-2.lock
  - rm -fr $HOME/.gradle/caches/*/plugin-resolution/
cache:
  directories:
    - $HOME/.gradle/caches/
    - $HOME/.gradle/wrapper/
```
{: data-file=".travis.yml"}

### Gradle daemon is disabled by default

[As recommended](https://docs.gradle.org/current/userguide/gradle_daemon.html) by the Gradle team,
the Gradle daemon is disabled by default.
If you would like to run `gradle` with daemon, add `--daemon` to the invocation.

## Projects Using Ant

### Default script Command

If Travis CI does not detect Maven or Gradle files it runs Ant:

```bash
ant test
```

### Dependency Management

Because there is no single standard way of installing project dependencies with Ant, you need to specify the exact command to run using `install:` key in your `.travis.yml`, for example:

```yaml
language: java
install: ant deps
```
{: data-file=".travis.yml"}

## Testing Against Multiple JDKs

To test against multiple JDKs, use the `jdk:` key in `.travis.yml`. For example, to test against Oracle JDKs 8 and 9, as well as OpenJDK 7:

```yaml
jdk:
  - oraclejdk8
  - oraclejdk9
  - openjdk7
```
{: data-file=".travis.yml"}

> Note that testing against multiple Java versions is not supported on OS X. See the [OS X Build Environment](/user/reference/osx/#JDK-and-OS-X) for more details.

Travis CI provides OpenJDK 7, Oracle JDK 8, and Oracle JDK 9.

### Updating Oracle JDK to a recent release

Your repository may require a newer release of Oracle JDK than the pre-installed version.
(You can consult [the list of published Oracle JDK packages](https://launchpad.net/~webupd8team/+archive/ubuntu/java).)

The following example will use the latest Oracle JDK 8:

```yaml
sudo: false
addons:
  apt:
    packages:
      - oracle-java8-installer
```
{: data-file=".travis.yml"}

## Build Matrix

For Java projects, `env` and `jdk` can be given as arrays
to construct a build matrix.

## Switching JDKs Within One Job

If your build needs to switch JDKs during a job, you can do so with `jdk_switcher use …`.

```yaml
script:
  - jdk_switcher use oraclejdk8
  - # do stuff with Java 8
  - jdk_switcher use openjdk7
  - # do stuff with Java 7
```
{: data-file=".travis.yml"}

Use of `jdk_switcher` also updates `$JAVA_HOME` appropriately.

## Examples

- [JRuby](https://github.com/jruby/jruby/blob/master/.travis.yml)
- [Riak Java client](https://github.com/basho/riak-java-client/blob/master/.travis.yml)
- [Cucumber JVM](https://github.com/cucumber/cucumber-jvm/blob/master/.travis.yml)
- [Symfony 2 Eclipse plugin](https://github.com/pulse00/Symfony-2-Eclipse-Plugin/blob/master/.travis.yml)
- [RESThub](https://github.com/resthub/resthub-spring-stack/blob/master/.travis.yml)
- [Joni](https://github.com/jruby/joni/blob/master/.travis.yml), JRuby's regular expression implementation
