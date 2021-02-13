# Validating Custom Fields

You can also tell the validation manager to **ONLY** validate on certain fields and not all the fields declared in the validation constraints.

```javascript
prc.results = validateModel( target=user, fields="login,password" );
```

This will only validate the `login` and `password` fields.

## Custom Includes/Excludes

You can also use the following arguments:

* `includeFields` : The fields to include in the validation ONLY
* `excludeFields` : The fields to exclude in the validation

```javascript
prc.results = validateModel( 
    target=user, 
    includeFields="username,password", 
    excludeFields="id" 
);
```

