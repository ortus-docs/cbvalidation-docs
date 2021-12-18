# Validating Nested Objects

We also have the ability to validate a nested object like a dependency or composed property and "bubble up" any validation error messages via the `errorMetadata` argument.

```javascript
// inject validationManager to handle nested validations
property name="validationManager" inject="validationManager@cbvalidation";

// root object constraints
this.constraints = {
    // validate a child object using a udf validator
    "childObject": { udf: function( value, target, errorMetadata ) {
            // validate the child object
            var validationResult = validationManager.validate( childObject );

            // if the child object has errors, populate the error metadata and return false
            if ( validationResult.hasErrors() ) {
                
                errorMetadata[ "customMessage" ] = "Child object failed validation! Reasons: ";
                
                // append each error message to our custom error message
                validationResult.getAllErrors().each( function( error, index ) {
                    errorMetadata[ "customMessage" ] &= "#index#. #error# ";
                } );

                return false;
            }
            
            return true; 
        },
        // the message replacement system will look for a matching metadata key and replace it with the message from the UDF.
        udfMessage: "{customMessage}" 
    };
}
```

The above code will validate a nested `ChildObject` property using the [UDF validator](../valid-constraints/README.md#udf).