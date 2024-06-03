---
author: Zain Ul Hassan
pubDatetime: 2024-02-23T23:52:17.000Z
title: Represent strings in android with kotlin sealed classes
featured: false
draft: true
tags:
  - compose
  - kotlin
  - android
  - string
description: A guide on representing strings in android using sealed & data classes in kotlin
---

```kotlin
sealed class Message {
    data class String(
        val value: String
    ) : Message

    data class Resource(
        @StringRes val id: Int
    ) : Message
}
```
