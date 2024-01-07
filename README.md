# How to set up different types of tests using Gradle and JVM Test Suite Plugin

Check `test-sets` branch to see shorter implementation.

The project contains 3 types of tests:
- Unit tests
- Integration tests
- End-to-end tests

First of all, you need to add plugin to `build.gradle` file:
```groovy
plugins {
    id 'jvm-test-suite'
}
```

Create a new folder in `src` to add your tests there.

    .
    ├── ...
    ├── src
    │   └── integrationTest
    │       └── com.habchak
    │           └── YourFunctionalTest.java
    └── ...

To add another type of test you need to specify it in `testing.suites` section of `build.gradle` file.

For instance, if you want to add a new type of test called `functional` you need to add the following line:
```groovy
testing {
    suites {
        test {
            useJUnitJupiter()
        }
        functionalTest(JvmTestSuite) {
            dependencies {
                implementation project()
            }
            targets {
                configureEach {
                    testTask.configure {
                        shouldRunAfter(test)
                    }
                }
            }
        }
    }
}
```

It's a good idea to make the `check` task depend on all types of tests because `build` runs `check`. 
```groovy
tasks.named('check') {
    dependsOn testing.suites.functionalTest
}
```

You can specify dependencies for each type of tests. 
```groovy
dependencies {
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.springframework.boot', module: 'spring-boot-test' //exclude from unit tests
    }
    integrationTestImplementation 'org.springframework.boot:spring-boot-starter-test'
    endToEndTestImplementation 'org.springframework.boot:spring-boot-starter-test'
    functionalTestImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```
