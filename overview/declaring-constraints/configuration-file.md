# Configuration File

You can optionally register shared constraints in your [ColdBox configuration](https://github.com/ortus/cbox-validation/tree/cc7e4d96663e1732860bcea678a632286d72e87e/Configuration/README.md) file under the `validation` directive. This means you register them with a **unique** **name** of your choice and its value is a collection of constraints for properties in your objects or forms. 

Later on you will reference the key **name** in your handlers or wherever in order to validate the object or form. Here is an example:

### Declaration

```javascript
validation = {
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

As you can see, our constraints definition describes the set of rules for a property on ANY target object or form.

### Usage

You can then use the keys for those constraints in the validation calls:

```javascript
validate( target, "sharedUser" );

validate( rc, "loginForm" );

validate( rc, "changePasswordForm" );
```

