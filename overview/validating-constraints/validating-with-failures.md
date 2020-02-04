# Validating With Failures

In **cbValidation** 1.5 we introduced the `validateOrFail()` function.  This function works in similar manner to the `validate()` method, but instead of giving you the results object, it throws an exception of type `ValidationException`.

| Incoming Target | Validation Fails | Result |
| :--- | :--- | :--- |
| Object | false | Returns the same object |
| Object | true | Throws `ValidationException` |
| Struct | false | Returns the structure with ONLY the fields that were validated from the constraints |
| Struct | true | Throws `ValidationException` |

## Exception Extended Info

So your validation fails, where are the results? In the exception structure under the `extendedInfo` key.  We store the validation results as JSON in the extended info and then you can use them for display purposes:

```javascript
try{
    validateOrFail( target );
    service.save( target );
} catch( ValidationException e  ){
    return {
        "error" : true,
        "validationErrors" : deserializeJSON( e.extendedInfo )
    };
}
```

 

