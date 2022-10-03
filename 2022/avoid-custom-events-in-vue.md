# Avoid Vue Component Events

Component events in Vue allow you to emit a customer event to the parent.

```typescript
<button @click=$emit('myEvent')>Im A Button</button>
```
