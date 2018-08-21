# Validating Custom Fields

You can also tell the validation manager to ONLY validate on certain fields and not all the fields declared in the validation constraints.

```javascript
prc.results = validateModel( target=user, fields="login,password" );
```

This will only validate the `login` and `password` fields.

