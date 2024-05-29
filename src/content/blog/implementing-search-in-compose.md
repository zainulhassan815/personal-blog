---
author: Zain Ul Hassan
pubDatetime: 2024-05-29T16:36:02.047Z
title: How to Implement Search in Compose
featured: true
draft: false
tags:
  - compose
  - kotlin
  - debounce
description: Implementing search functionality in compose for static data without view model
---

![Demo](../../assets/images/currency-converter-search-gif.gif)

In this tutorial, you will learn how to implement search functionality in compose for static data without using viewmodel. Let's start!

## Table of contents

## Scenario

We're building a [Currency Converter](https://github.com/zainulhassan815/currency-converter) app where user can select a currency he wants to convert. And for this purposoe, we show a dialog with a list of currencies and user can select any currency from the list. To further improve user experience, we'll add a search bar.

## Data Model

Before we begin, let's take a look at the data model that we have:

```kotlin
@Immutable
data class Currency(
    val code: String,
    val name: String,
    val symbol: String
)
```

We represent a single currency using this model. We will filter out this currencies by comparing the name and code with user's query.

## UI

```kotlin
@Composable
private fun CurrencyPickerContent(
    currencies: List<Currency>,
    onSelected: (currency: Currency) -> Unit,
    modifier: Modifier = Modifier,
    selectedCurrency: Currency? = null,
) {
    var query by remember { mutableStateOf("") }
    var filteredCurrencies by remember { mutableStateOf(currencies) }

    // TODO: Implement Search

    Surface(
        modifier = modifier,
        shape = RoundedCornerShape(8.dp)
    ) {
        LazyColumn(
            modifier = modifier.padding(8.dp),
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            stickyHeader {
                Surface(
                    modifier = Modifier.fillMaxWidth(),
                    color = MaterialTheme.colorScheme.surface
                ) {
                    TextField(
                        value = query,
                        onValueChange = { query = it },
                        shape = RoundedCornerShape(4.dp),
                        modifier = Modifier.padding(bottom = 8.dp)
                    )
                }
            }

            items(
                items = filteredCurrencies,
            ) {
                CurrencyCard(
                    currency = it,
                    onClick = { onSelected(it) },
                    modifier = Modifier.fillMaxWidth(),
                    selected = selectedCurrency?.code == it.code
                )
            }
        }
    }
}
```

Here we have a composable function that shows a list of currencies with a search bar at the top.

## Implementing Search

In our composable, we have two state variables:

- `query`: User search query. Initally an empty string.
- `filteredCurrencies`: filtered currencies based on user query. Initially set to given available currencies.

We can easily implement search by using Kotlin's flow api and LaunchedEffect:

```kotlin
LaunchedEffect(currencies) {
  snapshotFlow { query }
    .debounce(300)
    .mapLatest { it.lowercase().trim() }
    .distinctUntilChanged()
    .mapLatest {
        currencies.filter { currency ->
            currency.name.contains(it, ignoreCase = true) || currency.code.contains(it, ignoreCase = true)
        }
    }
    .flowOn(Dispatchers.IO)
    .collectLatest { filteredCurrencies = it }
}
```

We're using [snapshotFlow](https://developer.android.com/develop/ui/compose/side-effects#snapshotFlow) to convert user query into a flow and apply different flow operators such as `debounce`, `mapLatest` etc. At the end, we update the filtered currencies and the new currencies are displayed.

## Source Code

- Complete Code: [GitHub](https://github.com/zainulhassan815/currency-converter/blob/main/app/src/main/java/org/dreamerslab/currencyconverter/ui/home/CurrencyPickerDialog.kt#L66)
- Currency Converter Source Code: [GitHub](https://github.com/zainulhassan815/currency-converter)
