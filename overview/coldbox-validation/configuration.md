# Configuration

Here are the module settings you can place in your `ColdBox.cfc` by using the `validation` settings structure:

```javascript
validation = {
    // The third-party validation manager to use, by default it uses CBValidation.
    manager = "class path",
    // You can store global constraint rules here with unique names
    sharedConstraints = {
        name = {
            field = { constraints here }
        }
    }

}
```

| Key | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| `manager` | instantiation path or WireBox ID | false | `cbValidation.models.ValidationManager` | You can override the module manager with your own implementation. Just use an instantiation path or a valid WireBox object id. |
| `sharedConstraints` | struct | false | `{}` | This structure will hold all of your shared constraints for forms or/and objects. |

> **Important** The module will register several objects into WireBox using the `@cbvalidation` namespace. The validation manager is registered as `ValidationManager@cbvalidation`

