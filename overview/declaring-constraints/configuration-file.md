---
description: Shared Constraints
---

# Configuration File

You can optionally register constraints in your [ColdBox configuration](https://github.com/ortus/cbox-validation/tree/cc7e4d96663e1732860bcea678a632286d72e87e/Configuration/README.md) file under the `cbvalidation` directive. This means you register them with a **unique** **name** of your choice and its value is a collection of constraints for fields in your objects or forms. These will be called lovingly **Shared Constraints.**

Here is an example:

### Declaration

{% code title="config/ColdBox.cfc" %}
```javascript
cbvalidation = {
    sharedConstraints = {
        sharedUser = {
            fName = {required=true},
            lname = {required=true},
            age   = {required=true, max=18 }
            metadata = {required=false, type="json"}
        },
        loginForm = {
            username = {required=true}, password = {required=true}
        },
        changePasswordForm = {
            password = {required=true,min=6}, password2 = {required=true, sameAs="password", min=6}
        }
    }
}
```
{% endcode %}

As you can see, our constraints definition describes the set of rules for a property on ANY target object or form by unique key name.

### Usage

You can then use the keys for those constraints in the validation calls:

```javascript
validate( target=target, constraint="sharedUser" );

validate( target=rc, constraint="loginForm" );

validate( target=rc, constraint="changePasswordForm" );
```
