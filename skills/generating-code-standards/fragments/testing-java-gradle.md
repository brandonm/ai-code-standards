<!-- tier: project -->
<!-- requires: testing-universal -->

## Java/Gradle Testing — Stack-Specific Commands

### Running Tests

```bash
# Run all tests
./gradlew test

# Run tests + generate coverage report
./gradlew test jacocoTestReport

# Run tests + enforce coverage threshold
./gradlew test jacocoTestCoverageVerification
```

### JaCoCo Configuration

Add to `build.gradle` or `build.gradle.kts`:

<!-- WHY: Don't hardcode dependency versions in this template. Always look up the latest stable version at configuration time — stale versions in templates get cargo-culted silently. -->

```kotlin
plugins {
    id("jacoco")
}

jacoco {
    // Always check for the latest stable version before setting this.
    // Look up at: https://www.jacoco.org/jacoco/ or via MCP docs tools.
    toolVersion = "{{JACOCO_VERSION}}"
}

tasks.jacocoTestCoverageVerification {
    violationRules {
        rule {
            element = "PACKAGE"
            limit {
                counter = "LINE"
                minimum = "{{COVERAGE_THRESHOLD_DECIMAL}}".toBigDecimal()
            }
            limit {
                counter = "BRANCH"
                minimum = "{{COVERAGE_THRESHOLD_DECIMAL}}".toBigDecimal()
            }
        }
    }
}

tasks.jacocoTestReport {
    reports {
        html.required.set(true)
        xml.required.set(true)
        csv.required.set(true)
    }
}

// Always generate report after tests
tasks.test {
    finalizedBy(tasks.jacocoTestReport)
}
```

### Multi-Project Builds

For Gradle multi-project builds, use the aggregation plugin in the root `build.gradle.kts`:

```kotlin
plugins {
    id("jacoco-report-aggregation")
}

// Run from root: ./gradlew testCodeCoverageReport
```

### Report Locations

| Format | Path | Use |
|--------|------|-----|
| HTML | `build/reports/jacoco/test/html/index.html` | Human review |
| XML | `build/reports/jacoco/test/jacocoTestReport.xml` | CI tool integration |
| CSV | `build/reports/jacoco/test/jacocoTestReport.csv` | Agent parsing — easiest to read into tables |

### Integration Tests

Separate integration tests using a dedicated source set:

```kotlin
sourceSets {
    create("integrationTest") {
        compileClasspath += sourceSets.main.get().output
        runtimeClasspath += sourceSets.main.get().output
    }
}

tasks.register<Test>("integrationTest") {
    testClassesDirs = sourceSets["integrationTest"].output.classesDirs
    classpath = sourceSets["integrationTest"].runtimeClasspath
    shouldRunAfter(tasks.test)
}
```

### SAST for Java

```bash
# SpotBugs (static analysis)
./gradlew spotbugsMain

# Dependency vulnerability check
./gradlew dependencyCheckAnalyze
```
