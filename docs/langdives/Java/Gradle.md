# What is Gradle ?

### 1. **Gradle**
**Gradle** is a modern, powerful **build automation tool** used for building, testing, and deploying applications. It is particularly popular in **Java and Android projects** due to its flexibility and performance. Gradle uses a **Groovy/Kotlin-based DSL** to configure and manage builds, allowing for easy customization.

---

### 2. **How Gradle Works**

Gradle organizes builds using a **Directed Acyclic Graph (DAG)** of **tasks**, ensuring that tasks are executed only when necessary. The build process has three **phases**:
1. **Initialization Phase**:
   - Identifies the participating projects (useful for **multi-project builds**).
   - Creates the build environment (root project and sub-projects).
  
2. **Configuration Phase**:
   - Configures all tasks in the project(s).
   - Resolves dependencies, configures plugins, and defines task order.

3. **Execution Phase**:
   - Executes the required tasks in the correct sequence according to the task dependencies.

---

### 3. **Gradle Build Scripts**

Gradle build configuration is done in the **`build.gradle`** file. This file uses **Groovy** or **Kotlin DSL** to describe:
- Dependencies required for the project.
- Repositories where the dependencies will be fetched from.
- Plugins to extend functionality.
- Custom tasks to automate various steps.

**Example `build.gradle`**:
```groovy
plugins {
    id 'java'        // Apply Java plugin
    id 'application' // Allow running the app from CLI
}

repositories {
    mavenCentral() // Use Maven Central for dependencies
}

dependencies {
    implementation 'org.apache.commons:commons-lang3:3.12.0'  // Runtime dependency
    testImplementation 'junit:junit:4.13.2'  // Test dependency
}

application {
    mainClass = 'com.example.HelloWorld'  // Entry point of the app
}
```

---

### 4. **Key Sections of `build.gradle`**

#### 1. **Plugins Section**
```groovy
plugins {
    id 'java'
    id 'application'
}
```
- This section applies **Gradle plugins**. In the example, the `java` plugin sets up the project to be built as a **Java application**.
- The `application` plugin simplifies running the application with `gradle run`.

#### 2. **Repositories Section**
```groovy
repositories {
    mavenCentral()
}
```
- This section tells Gradle where to download dependencies from.
- **`mavenCentral()`** is a popular repository that hosts many open-source libraries.

#### 3. **Dependencies Section**
```groovy
dependencies {
    implementation 'org.apache.commons:commons-lang3:3.12.0'
    testImplementation 'junit:junit:4.13.2'
}
```
- Here, Gradle manages **external libraries** required by your project.
  - **`implementation`**: Adds a runtime dependency.
  - **`testImplementation`**: Adds a dependency for testing purposes.

#### 4. **Application Configuration**
```groovy
application {
    mainClass = 'com.example.HelloWorld'
}
```
- This specifies the **main class** to be run when using the `gradle run` command.

---


### 5. **Dependency Management in Gradle**

Gradle allows **automatic dependency management**. Dependencies (like libraries and frameworks) are fetched from **repositories** such as:
- **Maven Central**: A public repository.
- **JCenter**: (Now deprecated) Another widely-used repository.
- **Custom repositories**: Can be local or remote (e.g., Artifactory, Nexus).

Gradle **resolves dependencies in the following order**:
1. **Local Cache**: Located in `~/.gradle/caches/`.
2. **Local Maven Repository**: If `mavenLocal()` is specified (`~/.m2/repository/`).
3. **Remote Repositories**: Downloads dependencies from **Maven Central** or other remote repositories if not found locally.

**Offline Mode**:
```bash
gradle build --offline
```
- Forces Gradle to use only the **local cache**.

---

### 6. **Performance Benefits of Gradle**

Gradle is designed for speed and efficiency:
1. **Incremental Builds**: Only recompiles **changed files**, speeding up builds.
2. **Build Cache**: Gradle reuses **outputs of previous builds** if nothing has changed.
3. **Parallel Execution**: Gradle can execute tasks in **parallel** when there are no dependencies between them.

---

### 7. **Plugins in Gradle**

Gradle provides an ecosystem of **plugins** to extend functionality.  
- **Java Plugin**: Adds support for Java projects.
- **Application Plugin**: Allows running applications directly from Gradle.
- **Maven Publish Plugin**: Publishes artifacts to Maven repositories.
- **Android Plugin**: Used for Android app development.

Plugins are defined in the `build.gradle` file:
```groovy
plugins {
    id 'java'
    id 'maven-publish'
}
```

---

### 8. **Common Gradle Commands**

Here are some essential **Gradle commands** for working with projects:

| **Command**             | **Description**                                   |
|-------------------------|---------------------------------------------------|
| `gradle init`           | Initializes a new Gradle project.                 |
| `gradle build`          | Compiles, tests, and packages the project.        |
| `gradle run`            | Runs the application (if using the Application plugin). |
| `gradle clean`          | Removes the `build/` directory for a fresh build. |
| `gradle tasks`          | Lists all available tasks.                        |
| `gradle test`           | Runs all tests in the project.                    |
| `gradle publish`        | Publishes artifacts to Maven repositories.        |

---

### 9. **Multi-Project Builds with Gradle**

Gradle supports **multi-module projects** where different modules are part of the same build.

**Example Multi-Project Structure**:
```
/root-project
│
├── build.gradle            # Root project configuration
├── settings.gradle         # Lists sub-projects
├── module-1/
│   └── build.gradle        # Configuration for module 1
└── module-2/
    └── build.gradle        # Configuration for module 2
```

**`settings.gradle`** file:
```groovy
rootProject.name = 'multi-project-example'
include 'module-1', 'module-2'
```

**Running the build**:
```bash
gradle build
```
- This will build **all modules** in the correct order.

---

### 10. **Custom Tasks in Gradle**

Gradle allows developers to **create custom tasks** to automate specific workflows.

**Example Custom Task**:
```groovy
task hello {
    doLast {
        println 'Hello, Gradle!'
    }
}
```

Run the task:
```bash
gradle hello
```

Output:
```
Hello, Gradle!
```

Custom tasks can be **chained** and made dependent on other tasks:
```groovy
task compileCode {
    dependsOn clean
    doLast {
        println 'Compiling code...'
    }
}
```

---

### 11. **Publishing Artifacts with Gradle**

You can **publish your project’s artifacts** (e.g., JARs) to **Maven Local or Remote repositories** using the `maven-publish` plugin.

1. **Apply Maven Publish Plugin**:
   ```groovy
   plugins {
       id 'maven-publish'
   }
   ```

2. **Configure Publishing in `build.gradle`**:
   ```groovy
   publishing {
       publications {
           mavenJava(MavenPublication) {
               from components.java
           }
       }
       repositories {
           mavenLocal()  // Publish to local Maven repository (~/.m2/repository)
       }
   }
   ```

3. **Publish the Artifact**:
   ```bash
   gradle publishToMavenLocal
   ```
   This will install the JAR into your **local Maven repository**.

---


### 12. **Gradle Offline Mode**

Gradle can work **offline** to avoid unnecessary network calls.

Command:
```bash
gradle build --offline
```

- This mode ensures that Gradle **only uses the local cache** and does not try to access remote repositories.

---

### 13. **Gradle Wrapper: Key Points and Summary**

The **Gradle Wrapper** is a feature that allows a project to include a **specific Gradle version** along with scripts to execute builds. This ensures that anyone working on the project uses the **same Gradle version** without requiring a manual installation.

---

#### **What is the Gradle Wrapper?**

- The **Gradle Wrapper** consists of:
  - A set of **shell scripts** (Unix: `gradlew`, Windows: `gradlew.bat`).
  - A **configuration file** (`gradle/wrapper/gradle-wrapper.properties`) that specifies the Gradle version to use.
  - A **JAR file** (`gradle-wrapper.jar`) that downloads Gradle if needed.

- **Why Use It?**
  - Ensures that all team members use the **same Gradle version**.
  - Simplifies onboarding – contributors do not need to install Gradle manually.
  - Useful for **CI/CD pipelines** to avoid version mismatches.
  - Works **offline** after the first download by caching Gradle versions locally.

---

### 14. **Summary**

Gradle provides several advantages for **modern projects**:
- **Flexibility**: Customizable tasks and plugins allow you to tailor builds to your needs.
- **Speed**: Incremental builds and build caching reduce build times.
- **Dependency Management**: Easy integration with Maven repositories.
- **Scalability**: Suitable for **multi-project builds** and large teams.
- **Extensibility**: Works well for **Java, Android, and multi-language projects**.

---