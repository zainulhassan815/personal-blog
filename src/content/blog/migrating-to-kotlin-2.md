---
author: Zain Ul Hassan
pubDatetime: 2024-06-03T20:19:59.992Z
title: Migrating to Kotlin 2.0
featured: true
draft: false
tags:
  - kotlin
  - compose
  - ksp
description: Steps for migrating to latest Kotlin 2.0
---

Kotlin 2.0 is released and it brings a lot of cool new features and performance upgrades. We are going to migrate our project Currency Converter to use the latest Kotlin version 2.0. So let’s get started.

## Table of contents

## [Compose Compiler Plugin](https://developer.android.com/develop/ui/compose/compiler)

Previously, we had to check the version compatibility of both Kotlin and Compose Compiler as both were tightly coupled and only specific versions could work together. But now the [compose compiler is moving to the Kotlin repository](https://android-developers.googleblog.com/2024/04/jetpack-compose-compiler-moving-to-kotlin-repository.html), we don’t have to search for compatible versions. We can simply use the Kotlin version for compose compiler. But we have to do some setup in order to use the new compiler plugin.

### Set up with Gradle version catalogs

The following instructions outline how how can set up the Compose Compiler Gradle plugin:

1. In your `libs.versions.toml` file, remove any reference to the Compose compiler
2. In the plugins section, add the following new dependency

```toml
[versions]
kotlin = "2.0.0"

[plugins]
org-jetbrains-kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }

// Add this line
compose-compiler = { id = "org.jetbrains.kotlin.plugin.compose", version.ref = "kotlin" }

```

1. In your projects root `build.gradle.kts` file, add the following to the plugins section:

```kotlin
plugins {
   // Existing plugins
   alias(libs.plugins.compose.compiler) apply false
}

```

1. In each module that uses Compose, apply the plugin:

```kotlin
plugins {
   // Existing plugins
   alias(libs.plugins.compose.compiler)
}

```

Your app should now build and compile if you are using the default set up.

### Configuration options with the Compose Compiler Gradle Plugin

To configure the Compose compiler using the Gradle plugin, add the `composeCompiler` block to the module's `build.gradle.kts` file at the top level.

```kotlin
android { … }

composeCompiler {
    enableStrongSkippingMode = true

    reportsDestination = layout.buildDirectory.dir("compose_compiler")
    stabilityConfigurationFile = rootProject.layout.projectDirectory.file("stability_config.conf")
}

```

For the full list of available options, see the [documentation](https://www.jetbrains.com/help/kotlin-multiplatform-dev/compose-compiler.html#compose-compiler-options-dsl).

## Migrate from kapt to ksp

[Kotlin Symbol Processing (**_KSP_**)](https://kotlinlang.org/docs/ksp-overview.html) is an API that you can use to develop lightweight compiler plugins. KSP provides a simplified compiler plugin API that leverages the power of Kotlin while keeping the learning curve at a minimum. Compared to [kapt](https://kotlinlang.org/docs/kapt.html), annotation processors that use KSP can run up to two times faster.

1. Add KSP plugin to `libs.versions.toml` file.

```toml
[versions]
ksp = "2.0.0-1.0.21" # or the latest version

[plugins]
# existing plugins ...
google-devtools-ksp = { id = "com.google.devtools.ksp", version.ref = "ksp" }
```

1. In your projects root `build.gradle.kts` file, add the following to the plugins section:

```kotlin
plugins {
    // existing plugins
    alias(libs.plugins.google.devtools.ksp) apply false
}
```

1. In each module that uses KSP, apply the plugin:

```kotlin
plugins {
	// existing plugins
	alias(libs.plugins.google.devtools.ksp)
}

dependencies {
	// existing dependencies
	// Replace kapt("androidx.room:room-compiler:2.5.0") with
  ksp("androidx.room:room-compiler:2.5.0")
}
```

## Add .kotlin folder to .gitignore

Add this line to your _<root>/.gitignore_:

```
# Since Kotlin 2.0.0, folder named ".kotlin" is created at compile time
.kotlin
```

## Useful Links

- [Compose Compiler Gradle plugin](https://developer.android.com/develop/ui/compose/compiler)
- [Jetpack Compose compiler moving to the Kotlin repository​](https://android-developers.googleblog.com/2024/04/jetpack-compose-compiler-moving-to-kotlin-repository.html)
- [Migrate from kapt to KSP](https://developer.android.com/build/migrate-to-ksp)
