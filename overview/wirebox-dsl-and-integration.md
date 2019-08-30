# WireBox DSL & Integration

The module will register several objects into WireBox using the `@cbvalidation` namespace. The validation manager is registered as `ValidationManager@cbvalidation`, which is the one you can inject and use anywhere you like.

```javascript
// get reference
property name="validationManager" inject="ValidationManager@cbvalidation";
```

