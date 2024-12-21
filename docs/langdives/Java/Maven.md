# **Maven**

## **What is Maven ?**

Apache Maven is a build automation and project management tool primarily for Java projects. It uses XML (pom.xml) to describe the project's structure, dependencies, and build lifecycle. Maven focuses on the *“convention over configuration”* principle, meaning it provides a standard way to structure and build projects with minimal configuration.


## **How Maven Works ?**

Maven operates using a build lifecycle consisting of pre-defined phases. When you execute a specific phase, all preceding phases are executed as well.

???+ info "Maven Lifecycle Phases"
    | **Phase**       | **Description**                              |
    |-----------------|----------------------------------------------|
    | `validate`      | Validates the project structure.             |
    | `compile`       | Compiles the source code.                    |
    | `test`          | Runs the unit tests.                         |
    | `package`       | Packages the compiled code into a JAR or WAR.|
    | `verify`        | Verifies the package meets specifications.   |
    | `install`       | Installs the JAR into the local Maven repository. |
    | `deploy`        | Deploys the artifact to a remote repository. |

Maven revolves around the POM (Project Object Model), which defines:

- The project metadata (group ID, artifact ID, version).
- The dependencies required for the project.
- The build lifecycle and plugins to automate tasks like testing, packaging, and deploying.

We will understand `pom.xml` in next section more.


## **Understanding `pom.xml`**

The POM (Project Object Model) file is the heart of a Maven project. It defines dependencies, build plugins, and project metadata.

!!! example "Basic Example of pom.xml"
    ```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                                http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>

        <groupId>com.example</groupId>
        <artifactId>my-app</artifactId>
        <version>1.0-SNAPSHOT</version>
        <packaging>jar</packaging>

        <dependencies>
            <dependency>
                <groupId>org.apache.commons</groupId>
                <artifactId>commons-lang3</artifactId>
                <version>3.12.0</version>
            </dependency>
        </dependencies>

        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.1</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>
    ```

!!! info "Key Components of `pom.xml`"
    - **Group ID**: Represents the organization or group (e.g., `com.example`).
    - **Artifact ID**: Unique name for the project (e.g., `my-app`).
    - **Version**: Specifies the version (e.g., `1.0-SNAPSHOT`).
    - **Dependencies**: Lists external libraries needed for the project.
    - **Build Plugins**: Configures tasks such as compiling and packaging (e.g., `maven-compiler-plugin`).


## **Dependency Management**

Maven simplifies dependency management by automatically downloading required libraries from repositories.

!!! info "Scopes of Dependencies"
    - **`compile`**: Available at compile time and runtime (default scope).
    - **`test`**: Only used for testing purposes.
    - **`provided`**: Available at compile time but not packaged (e.g., Servlet API).
    - **`runtime`**: Available at runtime only.

!!! example "Example Dependency Declaration"
    ```xml
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.12.0</version>
    </dependency>
    ```


## **Maven Repositories**

Maven resolves dependencies from repositories:

- **Local Repository**: Located at `~/.m2/repository`, stores downloaded artifacts.
- **Central Repository**: Maven Central (public repository of libraries).
- **Remote Repository**: Custom/private repositories (e.g., Nexus, Artifactory).

!!! example "Adding a Custom Repository"
    ```xml
    <repositories>
        <repository>
            <id>my-repo</id>
            <url>https://my-repo-url</url>
        </repository>
    </repositories>
    ```


## **Maven Plugins**

Maven plugins extend its functionality. Plugins can handle tasks such as compiling, testing, or packaging.

!!! example "Maven Compiler Plugin"
    ```xml
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
    </plugin>
    ```
    This plugin ensures the source code is compiled with Java 8.


## **Maven Project Structure**

``` title="Maven recommended standard directory structure"
/my-project
│
├── pom.xml               # Project Object Model configuration
├── src
│   └── main
│       └── java          # Source code
│   └── test
│       └── java          # Unit tests
└── target                # Output directory (JAR, WAR)
```

- **`src/main/java`**: Contains the application source code.
- **`src/test/java`**: Contains unit test code.
- **`target/`**: Contains compiled artifacts (like JARs).

Maven supports multi-module projects, allowing multiple related projects to be managed together.

``` title="Directory Structure"
/parent-project
│
├── pom.xml (parent)
├── module-1/
│   └── pom.xml
└── module-2/
    └── pom.xml
```

```xml title="The parent pom.xml defines the modules:"
<modules>
    <module>module-1</module>
    <module>module-2</module>
</modules>
```

```bash title="Building all modules"
mvn install
```

## **Maven Wrapper (mvnw)**

Similar to Gradle, Maven has a wrapper (`mvnw`) that ensures the project uses a specific Maven version.

```bash title="Add Maven Wrapper"
mvn -N io.takari:maven:wrapper
```

- **Unix/Mac**: `./mvnw clean install`
- **Windows**: `mvnw.cmd clean install`


## **Maven Commands**

Here are some common Maven commands

| **Command**                  | **Description**                                  |
|------------------------------|--------------------------------------------------|
| `mvn compile`                | Compiles the source code.                        |
| `mvn test`                   | Runs unit tests.                                 |
| `mvn package`                | Packages the code into a JAR/WAR.                |
| `mvn install`                | Installs the artifact to the local repository.   |
| `mvn deploy`                 | Deploys the artifact to a remote repository.     |
| `mvn clean`                  | Cleans the `target/` directory.                  |
| `mvn dependency:tree`        | Displays the project's dependency tree.          |


## **Best Practices**

- Use Maven Wrapper for consistent builds.
- Store dependencies in a remote repository (like Nexus or Artifactory) for faster builds.
- Define dependency scopes carefully to avoid bloated artifacts.
- Use CI/CD pipelines (e.g., Jenkins, GitHub Actions) to automate builds and deployments.


## **Summary**

Maven is a mature, stable tool that simplifies building and managing Java applications. Its focus on conventions reduces the need for complex configurations, making it ideal for enterprise projects. While Maven may lack some of the flexibility and speed of Gradle, it is widely used in large organizations for its reliability and standardization. For projects requiring strict conventions and extensive dependency management, Maven remains a popular choice.

---