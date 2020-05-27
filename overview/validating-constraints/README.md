# Validating Constraints

## Validation Methods: `validate(), validateOrFail()`

Most likely you will be validating your objects at the controller layer in your ColdBox event handlers. All event handlers, layouts, views and interceptors have some new methods thanks to our module mixins.

```javascript
/**
 * Validate an object or structure according to the constraints rules.
 *
 * @target An object or structure to validate
 * @fields The fields to validate on the target. By default, it validates on all fields
 * @constraints A structure of constraint rules or the name of the shared constraint rules to use for validation
 * @locale The i18n locale to use for validation messages
 * @excludeFields The fields to exclude from the validation
 * @includeFields The fields to include in the validation
 * @profiles If passed, a list of profile names to use for validation constraints
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
 * @profiles If passed, a list of profile names to use for validation constraints
 *
 * @return The validated object or the structure fields that where validated
 * @throws ValidationException
 */
function validateOrFail()

/**
 * Retrieve the application's configured Validation Manager
 */
function getValidationManager()
```

You pass in your target object or structure, an optional list of fields or properties to validate only \(by default it does all of them\), and an optional constraints argument which can be the shared name or an actual constraints structure a-la-carte. If no constraints are passed, then we will look for the constraints in the target object as a public property called `constraints`. The `validate()` method returns a `cbvalidation.models.results.IValidationResult` type object, which you can then use for evaluating the validation.

```javascript
function saveUser( event, rc, prc ){
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

function save( event, rc, prc ){
    userService
        .getOrFail( rc.id )
        .populate()
        .validateOrFail()
        .save();
}
```

## Validation Results

The return of validate model is our results interface which has cool methods like and can be found under `cbvalidation.models.result.IValidationResult`

```javascript
/**
 * Copyright since 2020 by Ortus Solutions, Corp
 * www.ortussolutions.com
 * ---
 * The ColdBox validation results interface, all inspired by awesome Hyrule Validation Framework by Dan Vega
 */
import cbvalidation.models.result.*;
interface{

    /**
     * Add errors into the result object
     * @error The validation error to add into the results object
     * @error_generic IValidationError
     *
     * @return IValidationResult
     */
    any function addError(required error);

    /**
     * Set the validation target object name
     * @return IValidationResult
     */
    any function setTargetName(required string name);

    /**
     * Get the name of the target object that got validated
     */
    string function getTargetName();

    /**
     * Get the validation locale
     */
    string function getValidationLocale();

    /**
     * has locale information
     */
    boolean function hasLocale();

    /**
     * Set the validation locale
     *
     * @return IValidationResult
     */
    any function setLocale(required string locale);


    /**
     * Determine if the results had error or not
     * @fieldThe field to count on (optional)
     */
    boolean function hasErrors(string field);

    /**
     * Clear All errors
     * @return IValidationResult
     */
    any function clearErrors();


    /**
     * Get how many errors you have
     * @fieldThe field to count on (optional)
     */
    numeric function getErrorCount(string field);

    /**
     * Get the Errors Array, which is an array of error messages (strings)
     * @fieldThe field to use to filter the error messages on (optional)
     */
    array function getAllErrors(string field);

    /**
     * Get an error object for a specific field that failed. Throws exception if the field does not exist
     * @fieldThe field to return error objects on
     *
     * @return IValidationError[]
     */
    array function getFieldErrors(required string field);

    /**
     * Get a collection of metadata about the validation results
     */
    struct function getResultMetadata();

    /**
     * Set a collection of metadata into the results object
     *
     * @return IValidationResult
     */
    any function setResultMetadata(required struct data);

}
```

## Validation Error Object

Some of these methods return error objects which adhere to our Error Interface: `cbvalidation.models.result.IValidationError`, which can quickly tell you what field had the exception, what was the rejected value and the validation message:

```javascript
/**
 * Copyright since 2020 by Ortus Solutions, Corp
 * www.ortussolutions.com
 * ---
 * The ColdBox validation error interface, all inspired by awesome Hyrule Validation Framework by Dan Vega
 */
import cbvalidation.models.result.*;
interface {

    /**
     * Set the error message
     * @messageThe error message
     */
    IValidationError function setMessage( required string message );

    /**
     * Set the field
     * @messageThe error message
     */
    IValidationError function setField( required string field );

    /**
     * Set the rejected value
     * @valueThe rejected value
     */
    IValidationError function setRejectedValue( required any value );

    /**
     * Set the validator type name that rejected
     * @validationTypeThe name of the rejected validator
     */
    IValidationError function setValidationType( required any validationType );

    /**
     * Get the error validation type
     */
    string function getValidationType();

    /**
     * Set the validator data
     * @dataThe data of the validator
     */
    IValidationError function setValidationData( required any data );

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

}
```

