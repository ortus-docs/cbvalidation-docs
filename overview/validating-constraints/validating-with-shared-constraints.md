# Validating with shared constraints

We also have the ability to validate a target object or form with shared constraints from our configuration file. Just use the name of the key in the configuration form as the name of the `constraints` argument.

```javascript
    // validate user object
    prc.results = validateModel( target=user, constraints="sharedUser" );

    // validate incoming form elements in the RC or request collection
    prc.results = validateModel( target=rc, constraints="sharedUser" );
```

This will validate the object and `rc` using the `sharedUser` constraints defined in the [configuration file:](../declaring-constraints/configuration-file.md#declaration) `config/Coldbox.cfc`
