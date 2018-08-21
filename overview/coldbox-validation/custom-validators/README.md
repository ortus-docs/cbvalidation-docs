# Custom Validators

You can build also your own validators by implementing our interface `cbvalidaton.models.validators.IValidator` :

```javascript
/**
* Will check if an incoming value validates
* @validationResult.hint The result object of the validation
* @target.hint The target object to validate on
* @field.hint The field on the target object to validate on
* @targetValue.hint The target value to validate
*/
boolean function validate(required cbvalidation.models.result.IValidationResult validationResult, required any target, required string field, any targetValue, string validationData);

/**
* Get the name of the validator
*/
string function getName();
```

The arguments received are:

* `validationResults` : The validation result object
* `field` : The field or property in the object that is in validation
* `targetValue` : The value to test

Here is a sample validator:

```javascript
/**
********************************************************************************
Copyright Since 2005 ColdBox Framework by Luis Majano and Ortus Solutions, Corp
www.coldbox.org | www.luismajano.com | www.ortussolutions.com
********************************************************************************
The ColdBox validator interface, all inspired by awesome Hyrule Validation Framework by Dan Vega
*/
component accessors="true" implements="cbvalidation.models.validators.IValidator" singleton{

    property name="name";

    MaxValidator function init(){
        name = "Max";    
        return this;
    }

    /**
    * Will check if an incoming value validates
    * @validationResult.hint The result object of the validation
    * @target.hint The target object to validate on
    * @field.hint The field on the target object to validate on
    * @targetValue.hint The target value to validate
    * @validationData.hint The validation data the validator was created with
    */
    boolean function validate(required cbvalidation.models.result.IValidationResult validationResult, required any target, required string field, any targetValue, string validationData){

        // Simple Tests
        if( !isNull(arguments.targetValue) AND arguments.targetValue <= arguments.validationData ){
            return true;
        }

        var args = {message="The '#arguments.field#' value is not less than #arguments.validationData#",field=arguments.field,validationType=getName(),validationData=arguments.validationData};
        var error = validationResult.newError(argumentCollection=args).setErrorMetadata({max=arguments.validationData});
        validationResult.addError( error );
        return false;
    }

    /**
    * Get the name of the validator
    */
    string function getName(){
        return name;
    }

}
```

