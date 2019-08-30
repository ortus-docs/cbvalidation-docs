# Validating Constraints

Most likely you will be validating your objects at the controller layer in your ColdBox event handlers. All event handlers,layouts, views and interceptors have some new methods thanks to our module mixins.

```javascript
/**
* Validate an object or structure according to the constraints rules.
* @target An object or structure to validate
* @fields The fields to validate on the target. By default, it validates on all fields
* @constraints A structure of constraint rules or the name of the shared constraint rules to use for validation
* @locale The i18n locale to use for validation messages
* @excludeFields The fields to exclude in the validation
* 
* @return cbvalidation.model.result.IValidationResult
*/
function validate()

/**
 * Validate an object or structure according to the constraints rules and throw an exception if the validation fails.
 * The validation errors will be contained in the `extendedInfo` of the exception in JSON format
 *
 * @target An object or structure to validate
 * @fields The fields to validate on the target. By default, it validates on all fields
 * @constraints A structure of constraint rules or the name of the shared constraint rules to use for validation
 * @locale The i18n locale to use for validation messages
 * @excludeFields The fields to exclude from the validation
 * @includeFields The fields to include in the validation
 *
 * @return The validated object or the structure fields that where validated
 * @throws ValidationException
 */
function validateOrFail(){
	return getValidationManager().validateOrFail( argumentCollection=arguments );
}

/**
* Retrieve the application's configured Validation Manager
*/
function getValidationManager()
```

You pass in your target object or structure, an optional list of fields or properties to validate only \(by default it does all of them\), an an optional constraints argument which can be the shared name or an actual constraints structure a-la-carte. If no constraints are passed, then we will look for the constraints in the target object as a public property called `constraints`. The `validate()` method returns a `cbvalidation.models.results.IValidationResult` type object, which you can then use for evaluating the validation.

```javascript
function saveUser(event,rc,prc){
    // create and populate a user object from an incoming form
    var user = populateModel( entityNew("User") );
    // validate model
    prc.validationResults = validate( user );

    if( prc.validationResults.hasErrors() ){
        // show errors
    }
    else{
        // save
    }

}
```

The return of validate model is our results interface which has cool methods like:

```javascript
interface{

    /**
    * Add errors into the result object
    * @error.hint The validation error to add into the results object
    */
    IValidationResult function addError(required IValidationError error);

    /**
    * Set the validation target object name
    */
    IValidationResult function setTargetName(required string name);

    /**
    * Get the name of the target object that got validated
    */
    string function getTargetName();

    /**
    * Get the locale
    */
    string function getLocale();

    /**
    * has locale information
    */
    boolean function hasLocale();

    /**
    * Set the validation locale
    */
    IValidationResult function setLocale(required string locale);


    /**
    * Determine if the results had error or not
    * @field.hint The field to count on (optional)
    */
    boolean function hasErrors(string field);

    /**
    * Clear All errors
    */
    IValidationResult function clearErrors();


    /**
    * Get how many errors you have
    * @field.hint The field to count on (optional)
    */
    numeric function getErrorCount(string field);

    /**
    * Get the Errors Array, which is an array of error messages (strings)
    * @field.hint The field to use to filter the error messages on (optional)
    */
    array function getAllErrors(string field);

    /**
    * Get an error object for a specific field that failed. Throws exception if the field does not exist
    * @field.hint The field to return error objects on
    */
    IValidationError[] function getFieldErrors(required string field);

    /**
    * Get a collection of metadata about the validation results
    */
    struct function getResultMetadata();

    /**
    * Set a collection of metadata into the results object
    */
    IValidationResult function setResultMetadata(required struct data);

}
```

Some of these methods return error objects which adhere to our Error Interface: `cbvalidation.models.result.IValidationError`, which can quickly tell you what field had the exception, what was the rejected value and the validation message:

```javascript
/**
* Set error metadata that can be used in i18n message replacements or in views
* @data.hint The name-value pairs of data to store in this error.
*/
IValidationError function setErrorMetadata(required any data);

/**
* Get the error metadata
*/
struct function getErrorMetadata();
/**
* Set the error message
* @message.hint The error message
*/
IValidationError function setMessage(required string message);

/**
* Set the field
* @message.hint The error message
*/
IValidationError function setField(required string field);

/**
* Set the rejected value
* @value.hint The rejected value
*/
IValidationError function setRejectedValue(required any value);

/**
* Set the validator type name that rejected
* @validationType.hint The name of the rejected validator
*/
IValidationError function setValidationType(required any validationType);

/**
* Get the error validation type
*/
string function getValidationType();

/**
* Set the validator data
* @data.hint The data of the validator
*/
IValidationError function setValidationData(required any data);

/**
* Get the error validation data
*/
string function getValidationData();

/**
* Get the error message
*/
string function getMessage();

/**
* Get the error field
*/
string function getField();

/**
* Get the rejected value
*/
any function getRejectedValue();
```

