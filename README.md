# How to set up different types of tests using Gradle

This is a simple project to show how to set up different types of tests using Gradle and JVM Test Suite Plugin.

The project contains 3 types of tests:
- Unit tests
- Integration tests
- End-to-end tests

First of all, you need to add plugin to `build.gradle` file:
```
plugins {
    id 'jvm-test-suite'
}
```

To add another type of test you need to specify it in `testing.suites` section of `build.gradle` file.

For instance, if you want to add a new type of test called `functional` you need to add the following line:
```
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

Then you need to create a new folder `functionalTest.java` in `src` folder and add your tests there.

It's good idea to make 'check' task depend on all types of tests because 'build' runs 'check'. 
To do that you need to add the following line:
```
tasks.named('check') {
    dependsOn testing.suites.functionalTest
}
```

Also, you can specify dependencies for each type of tests. 
For instance, if you want to exclude Spring Boot Test for unit tests only:
```
dependencies {
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.springframework.boot', module: 'spring-boot-test' //exclude from unit tests
    }
    integrationTestImplementation 'org.springframework.boot:spring-boot-starter-test'
    endToEndTestImplementation 'org.springframework.boot:spring-boot-starter-test'
    functionalTestImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```