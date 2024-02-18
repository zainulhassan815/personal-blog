---
author: Zain Ul Hassan
pubDatetime: 2024-02-18T15:20:24.411Z
title: Modelling State in Kotlin
tags:
  - kotlin
  - android
  - compose
  - architecture
  - state
description: A simple guide on modelling state with in kotlin
---

State in an app is any value that can change over time. All Android apps display state to the user.
A few examples of state in Android apps:

- A Snackbar that shows when a network connection can't be established.
- A blog post and associated comments.
- Ripple animations on buttons that play when a user clicks them.
- Stickers that a user can draw on top of an image.

## Table of contents

Let's take a look at the gereal aspects of state:

## Generel aspects of state

An app can be in one of the following states:

- ### Initial State

The app is not in initial state and not performing any operations.

- ### Loading State

The app is fetching data to show to the user.

- ### Error State

The app has encounntered an error while fetching data.

- ### Success State

The app has successesfully fetched data and can present it to the user.

Now we know what state is and what are the general aspects of state, let's take a look at how we can model state:

## Modeling state with sealed & data classes

By using a combination of sealed & data classes, we can easily model state:

```kotlin
sealed class FeaturedContentUiState {
    data object Initial : FeaturedContentUiState()
    data object Loading : FeaturedContentUiState()
    data object Error : FeaturedContentUiState()
    data class Success(
        val movies: List<Movie> = emptyList()
    ) : FeaturedContentUiState()
}
```

I have used this approach in my PocketMovies app which you can find [here](https://github.com/zainulhassan815/pocketmovies/blob/main/app/src/main/java/org/dreamerslab/pocketmovies/ui/home/HomeScreenViewModel.kt#L23).

## Modeling state with data class only

You can also model state using only data classes:

```kotlin
data class FeaturedContentState(
    val isLoading: Boolean = false,
    val error: Throwable? = null,
    val data: List<Movie> = emptyList()
)
```

You can further enhance state class by adding more properties and defining an empty state:

```kotlin
data class FeaturedContentState(
    val isLoading: Boolean = false,
    val error: Throwable? = null,
    val data: List<String> = emptyList()
) {
    val hasError: Boolean
        get() = error != null

    companion object {
        val Empty = FeaturedContentState()
    }
}
```
