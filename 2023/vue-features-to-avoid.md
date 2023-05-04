# Vue Features to Avoid

## Plain Jain JavaScript

## Non-Pure Computed's

## Two-Way Binding

Two-way binding is error prone because you are mixing two responsibilities into a single feature. React avoids this issue entirely by not supporting two-way binding at all. Your Vue app should stick to normal input event handling for the following reasons:

- Not very well supported via intellisense
- Violates SRP
- Goes against the ethos of one-way data flow
- Less to learn, standard event handling is uniform across Vue, React, and Angular
- Little or zero support for side-effects such as validation

## Most Lifecycle Hooks

99% of the time you need a lifecycle hook it will be the [onMounted](https://vuejs.org/api/composition-api-lifecycle.html#onmounted) or [created](https://vuejs.org/api/options-lifecycle.html#created) hook for when you need to fetch data over the wire.

Your app does not need to monkey with any other lifecycle hooks. Vue is already doing all the heavy lifting for you.

## Direct DOM Manipulation

## Non-Callback State Manipulation

## Multi-Slot Projection

## Global Variables

## Features you SHOULD use

### Composition API

### Composable's

## Vue 80/20

Refs, Computed, Watchers, OnMounted, basic directives
