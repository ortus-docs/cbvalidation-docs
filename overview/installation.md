# Installation

## Instructions

Just drop into your **modules** folder or use [CommandBox](https://www.ortussolutions.com/products/commandbox) to install

`box install cbvalidation`

The module will register several objects into WireBox using the `@cbvalidation` namespace. The validation manager is registered as `ValidationManager@cbvalidation`. It will also register several helper methods that can be used throughout the ColdBox application.

## Mixins - Helper Methods

The module will also register two methods in your handlers/interceptors/layouts/views

* `validate()`
* `validateOrFail()`
* `getValidationManager()`

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

