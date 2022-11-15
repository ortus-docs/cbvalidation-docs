# Configuration

You can configure the module by creating a `cbvalidation` key in the `config/Coldbox.cfc` `moduleSettings` structure

{% code title="config/Coldbox.cfc" %}
```javascript
moduleSettings = {
    cbValidation = {
        // The third-party validation manager to use, by default it uses CBValidation.
        manager = "class path",
        // You can store global constraint rules here with unique names
        sharedConstraints = {
            name = {
                field = { constraints here }
            }
        }
    
    }
}
```
{% endcode %}

#### manager

The `manager` key by default points to `cbValidation.models.ValidationManager`.  If you would like to override or decorate our manager, then you can set the classpath of the manager to use.  This manager must adhere to our interface: `cbvalidation.interfaces.IValidationManager`

**sharedConstraints**

This structure will hold all of your shared constraints for forms or/and objects that you can easily reference by name.  It's like declaring the constraints inline but storing them globally.

{% hint style="danger" %}
**Important:** The module will register several objects into WireBox using the `@cbvalidation` namespace. The validation manager is registered as `ValidationManager@cbvalidation`
{% endhint %}
