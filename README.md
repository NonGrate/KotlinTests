## Kotlin tests

This repository shows the issue with tests written in Kotlin in a java module.

When you have a java module in the app and it is hidden in another directory:
```gradle
include ':lib'
project(':lib').projectDir = "$rootDir/libs/lib" as File
```
The source files are not located correctly and kotlin tests fail with the "Class not found: "" Empty test suite." error.

On Android Studio 3.0 beta-6 and earlier versions the IDE didn't knew about the kotlin output directory `build/classes/kotlin`, and was looking for the sources in `build/classes/java`.

Adding these tasks:

```gradle
task copyKotlinResources(type: Copy) {
    from "${buildDir}/classes/kotlin"
    into "${buildDir}/classes/java"
}

task removeKotlinResources(type: Delete) {
    delete "${buildDir}/classes/kotlin/"
}

processTestResources.dependsOn copyKotlinResources
copyKotlinResources.finalizedBy removeKotlinResources
```
Was solving the issue.

---

Android Studio 3.0-beta7 has this issue fixed. So no tasks are required.

---

Links:
- https://youtrack.jetbrains.com/issue/KT-17951
- https://issuetracker.google.com/issues/38454212
- https://issuetracker.google.com/issues/64804587
