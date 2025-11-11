# Java 25 Upgrade Documentation

This document describes the upgrade of the PhotoAlbum-Java application from Java 8 to Java 25.

## Upgrade Summary

- **Source Version**: Java 8 / Spring Boot 2.7.18
- **Target Version**: Java 25 / Spring Boot 3.4.0
- **Upgrade Tool Used**: OpenRewrite Maven Plugin
- **Date**: November 2025

## Changes Made

### 1. Added OpenRewrite Maven Plugin

Added the OpenRewrite Maven plugin to `pom.xml` with recipes for:
- `org.openrewrite.java.migrate.UpgradeToJava21` - Automated Java 21 migration
- `org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_4` - Automated Spring Boot 3.4 migration

### 2. Automated Code Modernizations

OpenRewrite automatically applied the following refactorings:

#### Java 11+ Features
- Replaced `!Optional.isPresent()` with `Optional.isEmpty()`

#### Java 15+ Features  
- Replaced `String.format()` with `.formatted()` method

#### Java 21+ Features
- Replaced `List.get(0)` with `List.getFirst()`

#### Jakarta EE Migration
- Migrated `javax.persistence.*` to `jakarta.persistence.*`
- Migrated `javax.validation.*` to `jakarta.validation.*`

### 3. Configuration Updates

#### pom.xml
```xml
<properties>
    <java.version>25</java.version>
    <maven.compiler.source>25</maven.compiler.source>
    <maven.compiler.target>25</maven.compiler.target>
</properties>

<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.4.0</version>
</parent>
```

#### Dockerfile
```dockerfile
FROM maven:3.9.6-eclipse-temurin-25 AS build
...
FROM eclipse-temurin:25-jre
```

### 4. File Changes

| File | Changes |
|------|---------|
| `pom.xml` | Updated Java version, Spring Boot version, added OpenRewrite plugin |
| `Dockerfile` | Updated base images to Java 25 |
| `src/main/java/com/photoalbum/model/Photo.java` | Migrated javax to jakarta imports |
| `src/main/java/com/photoalbum/controller/*.java` | Code modernizations (isEmpty, etc.) |
| `src/main/java/com/photoalbum/service/impl/*.java` | Code modernizations (formatted, getFirst) |

## Build Instructions

### Prerequisites
- Java 25 (Microsoft OpenJDK 25.0.1 or equivalent)
- Maven 3.9.x

### Compilation
```bash
export JAVA_HOME=/path/to/jdk-25
mvn clean compile
```

The project successfully compiles with Java 25 using:
```
javac [debug parameters release 25]
```

## Known Limitations

### Spring Boot Plugin Limitation
The Spring Boot Maven plugin (version 3.4.0) does not yet fully support Java 25's class file version 69. This affects the `repackage` goal which creates the executable JAR.

**Error**: `Unsupported class file major version 69`

**Workaround Options**:
1. Wait for Spring Boot 3.5.x or later which will add Java 25 support
2. Use Java 21 for packaging while developing with Java 25 features
3. Use alternative packaging methods (manual JAR creation)

This limitation is expected as Java 25 was just released as GA, and Spring Boot tooling typically lags behind new Java releases by a few months.

### Certificate Issues
When using Java 25, Maven may encounter certificate validation issues with some repositories. This is due to Java 25 having updated security policies. Use Java 17 or 21 for dependency downloads if needed.

## Testing

The code compiles successfully with Java 25. Full integration testing requires Spring Boot plugin updates to support Java 25's class file format.

## Migration Benefits

1. **Modern Language Features**: Access to all Java features through version 25
2. **Performance**: Java 25 includes performance improvements accumulated over many releases
3. **Security**: Latest security patches and improvements
4. **Long-term Support**: Using current Java keeps the codebase modern
5. **Pattern Matching**: Enhanced pattern matching for instanceof
6. **Record Patterns**: Improved data modeling with records
7. **Virtual Threads**: Better concurrency with Project Loom features

## Future Work

- Once Spring Boot 3.5+ is released with Java 25 support, update and test full build pipeline
- Review and adopt new Java 25-specific features in the codebase
- Update CI/CD pipelines to use Java 25
- Performance testing with Java 25 runtime

## References

- [OpenRewrite Documentation](https://docs.openrewrite.org/)
- [Java 25 Release Notes](https://jdk.java.net/25/)
- [Spring Boot 3.4 Documentation](https://docs.spring.io/spring-boot/docs/3.4.0/reference/html/)
- [Jakarta EE 10 Migration](https://jakarta.ee/)
