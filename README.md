# Gradle

## Introduction to Gradle
### What is Gradle?
Gradle is a build automation system. It helps you manage the entire lifecycle of your software project, from compiling source code to running tests, creating deployment artifacts (like JARs or WARs), and even publishing them to repositories. What sets Gradle apart is its incremental build capabilities, its support for multi-project builds, and its highly customizable nature.

#### How it Differs from Older Tools (Ant and Maven)
- **Apache Ant**:
  - **XML-based**: Ant uses XML to define build scripts. This can lead to verbose and less readable scripts, especially for complex projects.
  - **Procedural**: Ant builds are essentially a sequence of tasks executed in a defined order. It's like writing a script for a shell, but in XML.
  - **No Convention-over-Configuration**: Ant requires you to explicitly define every step of the build process.
  - **No Dependency Management**: Ant itself doesn't offer robust dependency management. You typically rely on external tools or manual processes.

- **Apache Maven**:
  - **XML-based (POM)**: Maven also uses XML, specifically a Project Object Model (POM) file (pom.xml), to define project configurations. While more structured than Ant, it can still be verbose.
  - **Convention-over-Configuration**: Maven strongly adheres to conventions. If your project follows the standard Maven directory structure, you need minimal configuration. This is a double-edged sword: great for standard projects, but rigid for unconventional ones.
  - **Declarative**: Maven is more declarative than Ant; you describe what you want to build, and Maven figures out how.
  - **Built-in Dependency Management**: Maven excels at dependency management, automatically downloading and managing project dependencies from remote repositories.

- **Gradle**:
  - **Groovy/Kotlin DSL**: Gradle uses a Groovy or Kotlin-based DSL for its build scripts. This provides a programmatic and highly expressive way to define your build logic, leading to more concise and readable scripts.
  - **Flexible**: While it offers conventions, Gradle is incredibly flexible. You can easily deviate from conventions and define custom build logic.
  - **Declarative** and Imperative: Gradle allows you to be both declarative (like defining dependencies) and imperative (writing custom tasks with Groovy/Kotlin code).
  - **Powerful Dependency Management**: Gradle's dependency management is robust, offering advanced features like dependency configurations, conflict resolution, and exclusion.
  - **Performance**: Gradle is designed for performance, utilizing features like the Gradle Daemon, build caching, and incremental builds to significantly speed up build times.

#### Why it's Preferred in Modern Java and Android Development
- **Performance**: Faster build times are crucial, especially in large projects and CI/CD pipelines. Gradle's performance optimizations provide a significant edge.
- **Flexibility and Customization**: The Groovy/Kotlin DSL allows developers to write complex, custom build logic tailored to specific project needs, something difficult to achieve with Ant or Maven's XML.
- **Rich Ecosystem**: Gradle has a vast ecosystem of plugins for various tasks, from compiling different languages to integrating with popular frameworks.
- **Excellent for Android**: Google officially adopted Gradle as the build system for Android, making it indispensable for Android development. Its multi-module support and Android-specific plugins are perfectly suited for mobile projects.
- **Scalability**: Gradle handles multi-module projects with ease, enabling large, complex applications to be broken down into manageable subprojects

---

## Setting Up a Gradle Project and Core Concepts
### Initializing a Gradle Project
You can initialize a new Gradle project using the gradle `init` command.
```bash
mkdir my-gradle-project
cd my-gradle-project
gradle init
```

The gradle `init` command will prompt you to choose the type of project (e.g., `application`, `library`, `java-library`), the language for your build script (Groovy or Kotlin), and other settings.

A typical `gradle init` output for a Java application might look like this:
```
Select type of project to generate:
  1: basic
  2: application
  3: library
  4: Gradle plugin
Enter choice [2]: 2

Select implementation language:
  1: C++
  2: Groovy
  3: Java
  4: Kotlin
  5: Swift
Enter choice [3]: 3

Select build script DSL:
  1: Groovy
  2: Kotlin
Enter choice [1]: 1

Select test framework:
  1: JUnit 4
  2: JUnit Jupiter
  3: TestNG
Enter choice [2]: 2

Project name (default: my-gradle-project):
Source package (default: my.gradle.project): com.example

Generate build using new APIs and behaviors? (some features may be incubating) (default: no) [yes, no]: no

BUILD SUCCESSFUL in 1s
2 actionable tasks: 2 executed
```

This will generate a basic project structure, including:
- `build.gradle` (or `build.gradle.kts`): The main build script.
- `settings.gradle` (or `settings.gradle.kts`): Defines the project structure for multi-module builds.
- `gradlew` and `gradlew.bat`: The Gradle Wrapper scripts.
- `src/main/java`, `src/test/java`: Standard source code and test code directories

### Build Scripts (`build.gradle` or `build.gradle.kts`)
The heart of a Gradle project is its build script. This is where you define how your project is built.
#### `build.gradle` (Groovy DSL)
This is the traditional and still widely used choice. Groovy is a dynamic language that allows for very concise and flexible scripts.
```groovy
plugins {
    id 'java' // Applies the Java plugin
    id 'application' // Applies the Application plugin
}

group 'com.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral() // Where Gradle looks for dependencies
}

dependencies {
    // Declare a dependency on the JUnit Jupiter testing library
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.10.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.10.0'

    // Declare a dependency for the main application
    implementation 'com.google.guava:guava:32.1.3-jre'
}

application {
    mainClass = 'com.example.App' // Specifies the main class for the application
}

test {
    useJUnitPlatform() // Configures JUnit Platform for tests
}
```

#### `build.gradle.kts` (Kotlin DSL)
Introduced to leverage the benefits of Kotlin, such as static typing, better IDE support (autocompletion, refactoring), and compile-time error checking. It generally offers a more robust and predictable build experience, especially for larger, more complex projects.

```kotlin
plugins {
    java // Applies the Java plugin
    application // Applies the Application plugin
}

group = "com.example"
version = "1.0-SNAPSHOT"

repositories {
    mavenCentral() // Where Gradle looks for dependencies
}

dependencies {
    // Declare a dependency on the JUnit Jupiter testing library
    testImplementation("org.junit.jupiter:junit-jupiter-api:5.10.0")
    testRuntimeOnly("org.junit.jupiter:junit-jupiter-engine:5.10.0")

    // Declare a dependency for the main application
    implementation("com.google.guava:guava:32.1.3-jre")
}

application {
    mainClass.set("com.example.App") // Specifies the main class for the application
}

tasks.test {
    useJUnitPlatform() // Configures JUnit Platform for tests
}
```

### Project Structure
Gradle projects follow a conventional structure, similar to Maven, but are flexible enough to accommodate variations

```
my-gradle-project/
├── .gradle/                  # Gradle's build cache and other internal files
├── .idea/                    # (Optional) IntelliJ IDEA project files
├── build/                    # Output directory for compiled classes, JARs, etc.
├── gradle/
│   └── wrapper/
│       ├── gradle-wrapper.jar  # Gradle Wrapper JAR
│       └── gradle-wrapper.properties # Gradle Wrapper configuration
├── gradlew                   # Linux/macOS Gradle Wrapper script
├── gradlew.bat               # Windows Gradle Wrapper script
├── settings.gradle           # Defines project hierarchy for multi-module builds
├── build.gradle              # Main build script for the root project
└── src/
    ├── main/
    │   ├── java/             # Main Java source code
    │   │   └── com/
    │   │       └── example/
    │   │           └── App.java
    │   └── resources/        # Main application resources
    └── test/
        ├── java/             # Test Java source code
        │   └── com/
        │       └── example/
        │           └── AppTest.java
        └── resources/        # Test resources
```

### Tasks and Lifecycle Phases
#### Tasks
A task is the smallest unit of work in Gradle. Everything Gradle does is executed by tasks. Examples of built-in tasks include `compileJava`, `test`, `jar`, `clean`, and `run`.
```bash
./gradlew tasks
```

#### Lifecycle Phases
Gradle's build process is divided into three distinct phases:
1. **Initialization Phase**: Determines which projects are involved in the build and creates a `Project` instance for each. This is where `settings.gradle` is evaluated.
2. **Configuration Phase**: Evaluates the `build.gradle` scripts of all projects involved in the build. During this phase, Gradle configures all tasks, creates the task graph (the order in which tasks need to be executed), and determines which tasks are up-to-date.
3. **Execution Phase**: Executes the tasks determined in the configuration phase. Gradle executes tasks in the order defined by the task graph, respecting dependencies between tasks. Up-to-date checks prevent unnecessary task execution

### Gradle Wrapper (`gradlew`)
The Gradle Wrapper is a script (`gradlew` for Linux/macOS, `gradlew.bat` for Windows) that provides a standardized way to execute Gradle builds. It ensures that everyone working on a project uses the same Gradle version, eliminating "it works on my machine" issues related to different Gradle installations.

When you run `./gradlew <task>`, the wrapper script first checks if the specified Gradle version is available. If not, it downloads it and then delegates the build execution to that specific Gradle version.

#### Why use the Wrapper?
- **Consistent Builds**: Guarantees that all developers and CI/CD environments use the exact same Gradle version.
- **No Manual Gradle Installation**: Users don't need to install Gradle globally; the wrapper handles it.
- **Simplified Project Setup**: Just clone the repo and run `./gradlew`.

---

## The Gradle Build Lifecycle in Detail
### Initialization Phase
During the Initialization Phase, Gradle decides which projects are part of the build. This phase is particularly important for multi-module projects.
- `settings.gradle` **Evaluation**: The `settings.gradle` file (or `settings.gradle.kts`) is evaluated here. This file defines the project hierarchy, including subprojects.
- **Project Instance Creation**: For each project declared in `settings.gradle`, Gradle creates a Project instance. These instances represent the projects in the build and hold their configuration.

#### Example `settings.gradle`
```groovy
rootProject.name = 'my-multi-project'
include 'app', 'library'
```

This tells Gradle that there's a root project named my-multi-project and has two separate named `app` and `library`

### Configuration Phase
This is arguably the most critical phase where Gradle determines what needs to be built and how.
- build.gradle Evaluation: Gradle evaluates the build.gradle (or build.gradle.kts) script for the root project and all included subprojects.

T**-ask Creation and Configuration**: As the build scripts are evaluated, tasks are created and configured. This includes defining task inputs, outputs, dependencies, and actions.

- **Task Graph Construction**: Gradle builds a Directed Acyclic Graph (DAG) of all tasks and their dependencies. This graph dictates the order in which tasks must be executed. If Task A depends on Task B, Task B will be executed before Task A.

- **Up-to-Date Checks**: During configuration, Gradle performs initial up-to-date checks. It looks at the inputs and outputs of tasks and determines if the task's outputs are up-to-date with respect to its inputs. If a task's outputs haven't changed since the last build and its inputs are the same, Gradle marks it as "UP-TO-DATE" and will skip its execution in the next phase

**Important Note**: Any logic directly in the build.gradle script (outside of task definitions) is executed during the configuration phase. This can sometimes lead to unexpected behavior or performance issues if complex logic is placed directly in the top-level script, as it will run even if the associated tasks are up-to-date.

### Execution Phase
In the Execution Phase, Gradle executes the tasks in the order determined by the task graph.

- **Task Execution**: For each task in the task graph that is not marked as "UP-TO-DATE", Gradle executes its defined actions.
- **Parallel Execution (Optional)**: If configured, Gradle can execute independent tasks in parallel, significantly speeding up build times.
- **Build Caching**: Gradle leverages a build cache (local and/or remote) to store outputs of tasks. If a task's outputs are found in the cache, Gradle can retrieve them instead of re-executing the task, even across different machines or projects.
- **Incremental Builds**: Gradle's up-to-date checks and incremental compilation/testing ensure that only necessary tasks and parts of tasks are re-executed when changes occur, leading to faster rebuilds.

---

## Defining and Executing Custom Tasks
### Defining Custom Tasks
You can define custom tasks in your `build.gradle` (Groovy DSL) or `build.gradle.kts` (Kotin DSL)

#### Defining Custom Tasks
```groovy
// Simple task
task hello {
    doLast {
        println 'Hello from a custom task!'
    }
}

// Task with properties and input/output
task generateReport(type: MyReportTask) {
    inputFile = file('src/main/resources/data.txt')
    outputDir = file("$buildDir/reports")
}

// Define a custom task type (often in buildSrc or a custom plugin)
class MyReportTask extends DefaultTask {
    @InputFile
    File inputFile

    @OutputDirectory
    File outputDir

    @TaskAction
    void generate() {
        println "Generating report from ${inputFile.name} to ${outputDir.name}"
        outputDir.mkdirs()
        new File(outputDir, "report.txt").text = "Report generated from ${inputFile.name}"
    }
}
```

### Kotlin DSL
```kotlin
// Simple task
tasks.register("hello") {
    doLast {
        println("Hello from a custom task!")
    }
}

// Task with properties and input/output
tasks.register<MyReportTask>("generateReport") {
    inputFile.set(layout.projectDirectory.file("src/main/resources/data.txt"))
    outputDir.set(layout.buildDirectory.dir("reports"))
}

// Define a custom task type (often in buildSrc or a custom plugin)
// This usually goes in `buildSrc/src/main/kotlin/MyReportTask.kt`
open class MyReportTask : DefaultTask() {
    @get:InputFile
    val inputFile: RegularFileProperty = project.objects.fileProperty()

    @get:OutputDirectory
    val outputDir: DirectoryProperty = project.objects.directoryProperty()

    @TaskAction
    fun generate() {
        val inputFileName = inputFile.get().asFile.name
        val outputDirectory = outputDir.get().asFile
        println("Generating report from $inputFileName to ${outputDirectory.name}")
        outputDirectory.mkdirs()
        outputDirectory.resolve("report.txt").writeText("Report generated from $inputFileName")
    }
}
```

#### Executing Custom Tasks
```bash
./gradlew hello
./gradlew generateReport
```

### Task Dependencies
Tasks often depend on other tasks to complete their work. Gradle provides several ways to define these dependencies:
#### `dependsOn`
The most common way to define a dependency. The dependent task will execute before the task declaring `dependsOn`
```groovy
task cleanAndBuild {
    dependsOn 'clean', 'build'
    doLast {
        println 'Cleaned and built!'
    }
}
```

```kotlin
tasks.register("cleanAndBuild") {
    dependsOn("clean", "build")
    doLast {
        println("Cleaned and built!")
    }
}
```

#### `mustRunAfter`
Specifies an ordering constraint. The current task must run after the specified task, but it doesn't imply a direct dependency. It's used when you want a specific order without forcing the other task to run if it's not needed.
```groovy
task deploy {
    mustRunAfter 'test'
    doLast {
        println 'Deploying application...'
    }
}
```

```kotlin
tasks.register("deploy") {
    mustRunAfter(tasks.test)
    doLast {
        println("Deploying application...")
    }
}
```

#### `shouldRunAfter`
Similar to `mustRunAfter` but a weaker ordering constraint. It's a hint for Gradle's scheduler

### Incremental Builds and Up-to-Date Checks
Gradle significantly speeds up builds by avoiding unnecessary work through **incremental builds** and **up-to-date checks**.
- **Inputs and Outputs**: Tasks declare their inputs (source files, configuration files) and outputs (compiled classes, JARs, reports).
- **Snapshots**: Gradle takes a "snapshot" of a task's inputs and outputs before and after execution.
- **Up-to-Date Check**: On subsequent builds, Gradle compares the current snapshot of inputs with the previous one. If the inputs haven't changed and the outputs still exist, the task is considered "UP-TO-DATE" and is skipped.
- **Build Cache**: If a task has already been executed with the same inputs on another machine or in a previous build, Gradle can retrieve its outputs from the build cache, further optimizing build times.

To leverage incremental builds effectively for custom tasks, you must correctly annotate task properties with `@Input`, `@Output`, `@InputFile`, `@InputDirectory`, `@OutputFiles`, `@OutputDirectory`, etc. This tells Gradle which files and properties contribute to the task's state for up-to-date checks.

---

## Dependency Management
### Declaring Dependencies in `dependencies {}` Block
Dependencies are declared within the `dependencies {}` block in your `build.gradle` file.
```groovy
dependencies {
    // Compile-time dependencies
    implementation 'org.springframework.boot:spring-boot-starter-web:3.2.0'
    implementation 'com.google.guava:guava:32.1.3-jre'

    // Test dependencies
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.10.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.10.0'

    // Runtime-only dependencies
    runtimeOnly 'mysql:mysql-connector-java:8.0.33'

    // Provided dependencies (not packaged in WAR/JAR)
    compileOnly 'javax.servlet:javax.servlet-api:4.0.1'

    // Project dependencies (for multi-module projects)
    implementation project(':my-library')
}
```

```kotlin
dependencies {
    // Compile-time dependencies
    implementation("org.springframework.boot:spring-boot-starter-web:3.2.0")
    implementation("com.google.guava:guava:32.1.3-jre")

    // Test dependencies
    testImplementation("org.junit.jupiter:junit-jupiter-api:5.10.0")
    testRuntimeOnly("org.junit.jupiter:junit-jupiter-engine:5.10.0")

    // Runtime-only dependencies
    runtimeOnly("mysql:mysql-connector-java:8.0.33")

    // Provided dependencies (not packaged in WAR/JAR)
    compileOnly("javax.servlet:javax.servlet-api:4.0.1")

    // Project dependencies (for multi-module projects)
    implementation(project(":my-library"))
}
```

### Configuration (Scopes)
Gradle uses **configurations** (also known as dependency scopes in Maven) to manage when a dependency is available. Common configurations provided by the **java** plugin include:
- `implementation`: Dependencies required to compile the production source code. These dependencies are included in the compile classpath but are not exposed to consumers of your library (i.e., they are not part of the API classpath). This helps with faster compilation and better encapsulation.
- `api`: (For libraries) Dependencies that are required to compile the production source code and are part of the public API of your library. When your library is consumed by another project, `api` dependencies will be on that project's compile classpath.
- `compileOnly`: Dependencies required only at compile time and not at runtime. They are typically provided by the runtime environment (e.g., a servlet container providing `javax.servlet-api`). These are not included in the final artifact.
- `runtimeOnly`: Dependencies that are only needed at runtime and not at compile time. They are packaged with the final artifact (e.g., JDBC drivers).
- `testImplementation`: Dependencies required to compile and run tests.
- `testRuntimeOnly`: Dependencies only needed at runtime for tests.
- `developmentOnly`: (Often used with Spring Boot) Dependencies that are only needed during development time, like `spring-boot-devtools`

### Transitive Dependencies, Version Conflict Resolution, and Dependency Exclusions
- **Transitive Dependencies**: When you declare a dependency, Gradle automatically downloads its dependencies, and those dependencies' dependencies, and so on. This is called **transitive dependency management**. It simplifies dependency declaration but can lead to dependency hell if not managed properly.
- **Version Conflict Resolution**: If different dependencies require different versions of the same transitive dependency, Gradle applies a **dependency resolution strategy**. By default, Gradle chooses the **latest version** found on the classpath

You can influence this behavior:
#### Force a version
```groovy
configurations.all {
    resolutionStrategy {
        force 'com.fasterxml.jackson.core:jackson-databind:2.15.2'
    }
}
```

```kotlin
configurations.all {
    resolutionStrategy {
        force("com.fasterxml.jackson.core:jackson-databind:2.15.2")
    }
}
```

#### Prefer a version
```groovy
configurations.all {
    resolutionStrategy {
        prefer 'com.google.guava:guava:32.1.3-jre'
    }
}
```

```kotlin
configurations.all {
    resolutionStrategy {
        prefer("com.google.guava:guava:32.1.3-jre")
    }
}
```

- **Dependency Exclusions**: Sometimes, you want to exclude a specific transitive dependency from being pulled in by another dependency. This is useful for avoiding conflicts or including a different version.
```groovy
implementation('org.springframework.boot:spring-boot-starter-web:3.2.0') {
    exclude group: 'org.apache.logging.log4j', module: 'log4j-to-slf4j'
}
```

```kotlin
implementation("org.springframework.boot:spring-boot-starter-web:3.2.0") {
    exclude(group = "org.apache.logging.log4j", module = "log4j-to-slf4j")
}
```

You can inspect the dependency tree to understand what's being pulled in:
```bash
./gradle dependencies
```

### How Gradle Resolved Dependencies from Repositories
Gradle resolves dependencies by searching for them in defined `repositories`.
- `mavenCentral()`: The most common and default public Maven repository.
- `google()`: Specifically for Android development, this repository hosts Android-specific libraries.
- `jcenter()`: (Deprecated, but historically used) Another public Maven repository.
- **Custom Repositories (Nexus, Artifactory)**: For private artifacts or internal company libraries, you'll configure custom Maven or Ivy repositories

```groovy
repositories {
    mavenCentral()
    google() // For Android projects
    maven {
        url 'https://repo.mycompany.com/maven-releases' // Custom private repository
        credentials {
            username 'user'
            password 'password'
        }
    }
}
```

```kotlin
repositories {
    mavenCentral()
    google() // For Android projects
    maven {
        url = uri("https://repo.mycompany.com/maven-releases") // Custom private repository
        credentials {
            username = "user"
            password = "password"
        }
    }
}
```

Gradle searches repositories in the order they are declared. If a dependency is found in an earlier repository, later ones are not checked for that specific dependency.

---

## Multi-Module Projects
Gradle excels at managing **multi-module projects** (also known as multi-project builds). This allows you to break down large applications into smaller, more manageable, and reusable subprojects.

### Creating a Root Project with Multiple Subprojects
A multi-module project typically has a root project and several subprojects. The `settings.gradle` file defines this structure.

### Project Structure
```
my-multi-project/
├── settings.gradle
├── build.gradle              # Root project's build script (often empty or for common config)
├── app/                      # Subproject 1 (e.g., main application)
│   └── build.gradle
│   └── src/
└── library/                  # Subproject 2 (e.g., reusable library)
    └── build.gradle
    └── src/
```

#### `settings.gradle`
```groovy
rootProject.name = 'my-multi-project'
include 'app', 'library'
```

```kotlin
rootProject.name = "my-multi-project"
include("app", "library")
```

### Sharing Configurations Acrss Modules
One of the benefits of multi-module builds is sharing common configurations, plugins, and dependencies.
- Root `build.gradle`: You can apply plugins and define common configurations in the root project's `build.gradle` file.
- `allprojects {}` and `subprojects {}` blocks:
  - `allprojects {}`: Applies configurations to the root project and all subprojects.
  - `subprojects {}`: Applies configurations only to the subprojects.

```groovy
plugins {
    // Apply plugins common to all projects or the root project itself
    id 'java-base' // For shared Java-related config
}

allprojects {
    repositories {
        mavenCentral()
    }
    group = 'com.example.multi'
    version = '1.0-SNAPSHOT'
}

subprojects {
    apply plugin: 'java' // Apply Java plugin to all subprojects
    apply plugin: 'idea' // Apply Idea plugin to all subprojects

    // Common test dependencies for all subprojects
    dependencies {
        testImplementation 'org.junit.jupiter:junit-jupiter-api:5.10.0'
        testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.10.0'
    }
}
```

```kotlin
plugins {
    // Apply plugins common to all projects or the root project itself
    base // For shared Java-related config (equivalent to 'java-base')
}

allprojects {
    repositories {
        mavenCentral()
    }
    group = "com.example.multi"
    version = "1.0-SNAPSHOT"
}

subprojects {
    apply(plugin = "java") // Apply Java plugin to all subprojects
    apply(plugin = "idea") // Apply Idea plugin to all subprojects

    // Common test dependencies for all subprojects
    dependencies {
        testImplementation("org.junit.jupiter:junit-jupiter-api:5.10.0")
        testRuntimeOnly("org.junit.jupiter:junit-jupiter-engine:5.10.0")
    }
}
```

### Inter-project Dependencies
Subprojects can depend on each other. This is crucial for modular applications.

#### `app/build.gradle`
```groovy
plugins {
    id 'application'
}

dependencies {
    implementation project(':library') // 'app' depends on 'library'
}

application {
    mainClass = 'com.example.app.App'
}
```

#### `app/build.gradle.kts`
```kotlin
plugins {
    id 'application'
}

dependencies {
    implementation project(':library') // 'app' depends on 'library'
}

application {
    mainClass = 'com.example.app.App'
}
```