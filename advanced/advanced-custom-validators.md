# Custom Validators

If the core validators are not sufficient for you, then you can create your own custom validators. You can either leverage the `udf` validator and create your own closure/lambda to validate inline or create a reusable validator CFC

## Closure/Lambda Validator

If you use the `udf` validator, then you can declare your validation inline. Just create a closure/lambda that will be called for you at the time of validation. This closure/lambda will receive all the following arguments and MUST return a boolean indicator: **true** => passed, **false** => invalid

* `value` : The value to validate, can be null
* `target` : The object that is the target of validation

```javascript
slug : {
    required : true,
    udf : ( value, target ) => {
        if( isNull( arguments.value ) ) return false;
        return qb.from( "content" )
            .where( "slug", arguments.value )
            .when( this.isLoaded(), ( q ) => {
                arguments.q.whereNotIn( "id", this.getId() );
            } )
            .count() == 0;
    }
},
```

## Custom CFC Validator

You can also create a reusable CFC that can be shared in any ColdBox app as a validator. Create the CFC and it should implement our interface which can be found here: `cbvalidation.models.validators.IValidator` and it specifies just two functions your own validator must implement: `getName(), validate():`

{% code title="cbvalidation.models.validators.IValidator" %}
```java
/**
 * Copyright since 2020 by Ortus Solutions, Corp
 * www.ortussolutions.com
 * ---
 * The ColdBox validator interface, all inspired by awesome Hyrule Validation Framework by Dan Vega
 */
interface {

    /**
     * Will check if an incoming value validates
     * @validationResultThe result object of the validation
     * @targetThe target object to validate on
     * @fieldThe field on the target object to validate on
     * @targetValueThe target value to validate
     * @rules The rules imposed on the currently validating field
     */
    boolean function validate(
        required any validationResult,
        required any target,
        required string field,
        any targetValue,
        any validationData,
        struct rules
    );

    /**
     * Get the name of the validator
     */
    string function getName();

}
```
{% endcode %}

Here is a sample validator:

```javascript
/**
 * Copyright since 2020 by Ortus Solutions, Corp
 * www.ortussolutions.com
 * ---
 * This validator validates if a value is is less than a maximum number
 */
component accessors="true" singleton {

    property name="name";

    /**
     * Constructor
     */
    MaxValidator function init(){
        variables.name = "Max";
        return this;
    }

    /**
     * Will check if an incoming value validates
     * @validationResultThe result object of the validation
     * @targetThe target object to validate on
     * @fieldThe field on the target object to validate on
     * @targetValueThe target value to validate
     * @validationDataThe validation data the validator was created with
     */
    boolean function validate(
        required any validationResult,
        required any target,
        required string field,
        any targetValue,
        any validationData,
        struct rules
    ){
        // return true if no data to check, type needs a data element to be checked.
        if ( isNull( arguments.targetValue ) || ( isSimpleValue( arguments.targetValue ) && !len( arguments.targetValue ) ) ) {
            return true;
        }

        // Max Tests
        if ( arguments.targetValue <= arguments.validationData ) {
            return true;
        }

        var args = {
            message        : "The '#arguments.field#' value is not less than or equal to #arguments.validationData#",
            field          : arguments.field,
            validationType : getName(),
            rejectedValue  : ( isSimpleValue( arguments.targetValue ) ? arguments.targetValue : "" ),
            validationData : arguments.validationData
        };
        var error = validationResult.newError( argumentCollection = args ).setErrorMetadata( { max : arguments.validationData } );
        validationResult.addError( error );
        return false;
    }

    /**
     * Get the name of the validator
     */
    string function getName(){
        return variables.name;
    }

}
```

## Defining Custom Validators

You can use them in two approaches when defining them in your constraints:

1. Use the `validator` constraints which points to the Wirebox ID of your own custom validator object. Please note that if you use this approach you will not be able to pass validation data into the validator.
2. Use the WireBox ID as they key of your validator. Then you can pass your own validation data into the validator.

{% hint style="success" %}
Approach number 2 is much more flexible as it will allow you to declare multiple custom validators and each of those validators can receive validation data as well.
{% endhint %}

```javascript
//sample custom validator constraints
    this.constraints = {
        // Approach #1
        myField = {
            required : true,
            validator : "MyCustomID"
        },

        // Approach #2
        myField2 = {
            required : true,
            UniqueInMyDatabase : {
                column : "column_name",
                table : "table_name",
                dsn : "myDatasource"
            },
            MyTimezoneValidator : true
        }
     };
```

{% hint style="success" %}
If you don't have any validation data to pass to a validator, just pass an empty struct (`{}`) or an empty string
{% endhint %}

## Error Metadata Integration

Starting with CBValidation 4.3.0, both UDF and Method validators support **error metadata** - a powerful feature that allows custom validators to provide additional contextual information about validation failures. This metadata can be used for i18n message replacements, enhanced error display, or custom error handling logic.

### How Error Metadata Works

When using UDF or Method validators, your validation function receives an additional `errorMetadata` argument (passed by reference) that you can populate with custom data when validation fails.

{% tabs %}

{% tab title="BoxLang" %}

{% code title="User.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
// UDF Validator with Error Metadata
class {
    property name="email";
    property name="age";

    this.constraints = {
        email: {
            required: true,
            udf: (value, target, errorMetadata) => {
                if (isNull(arguments.value)) return false;

                // Check if email already exists in database
                var existingUser = userService.findByEmail(arguments.value);
                if (!isNull(existingUser)) {
                    // Populate error metadata with additional context
                    arguments.errorMetadata.duplicateUserId = existingUser.getId();
                    arguments.errorMetadata.existingSince = existingUser.getCreatedDate();
                    arguments.errorMetadata.conflictType = "email_duplicate";
                    return false;
                }
                return true;
            }
        },

        age: {
            required: true,
            method: "validateAgeRange"  // Method validator example
        }
    };

    // Method validator that uses error metadata
    boolean function validateAgeRange(value, errorMetadata) {
        if (isNull(arguments.value)) return false;

        var minAge = 18;
        var maxAge = 65;

        if (arguments.value < minAge) {
            arguments.errorMetadata.minimumRequired = minAge;
            arguments.errorMetadata.provided = arguments.value;
            arguments.errorMetadata.category = "age_too_young";
            return false;
        }

        if (arguments.value > maxAge) {
            arguments.errorMetadata.maximumAllowed = maxAge;
            arguments.errorMetadata.provided = arguments.value;
            arguments.errorMetadata.category = "age_too_old";
            return false;
        }

        return true;
    }
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="User.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
// UDF Validator with Error Metadata
component {
    property name="email";
    property name="age";

    this.constraints = {
        email = {
            required = true,
            udf = function( value, target, errorMetadata ) {
                if ( isNull( arguments.value ) ) return false;

                // Check if email already exists in database
                var existingUser = userService.findByEmail( arguments.value );
                if ( !isNull( existingUser ) ) {
                    // Populate error metadata with additional context
                    arguments.errorMetadata.duplicateUserId = existingUser.getId();
                    arguments.errorMetadata.existingSince = existingUser.getCreatedDate();
                    arguments.errorMetadata.conflictType = "email_duplicate";
                    return false;
                }
                return true;
            }
        },

        age = {
            required = true,
            method = "validateAgeRange"  // Method validator example
        }
    };

    // Method validator that uses error metadata
    boolean function validateAgeRange( value, errorMetadata ) {
        if ( isNull( arguments.value ) ) return false;

        var minAge = 18;
        var maxAge = 65;

        if ( arguments.value < minAge ) {
            arguments.errorMetadata.minimumRequired = minAge;
            arguments.errorMetadata.provided = arguments.value;
            arguments.errorMetadata.category = "age_too_young";
            return false;
        }

        if ( arguments.value > maxAge ) {
            arguments.errorMetadata.maximumAllowed = maxAge;
            arguments.errorMetadata.provided = arguments.value;
            arguments.errorMetadata.category = "age_too_old";
            return false;
        }

        return true;
    }
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

### Accessing Error Metadata

Once validation fails and metadata is populated, you can access it through the `ValidationError` object:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="ValidationExample.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
// Validate and handle errors with metadata
var result = validate(target=user, constraints="userValidation");

if (result.hasErrors()) {
    for (var error in result.getAllErrors()) {
        var metadata = error.getErrorMetadata();

        // Use metadata for enhanced error handling
        switch (metadata.category ?: "") {
            case "email_duplicate":
                // Show specific message about existing user
                flash.put("error", "This email was already registered on " &
                    dateFormat(metadata.existingSince, "mm/dd/yyyy"));
                break;

            case "age_too_young":
                // Age-specific guidance
                flash.put("error", "You must be at least " &
                    metadata.minimumRequired & " years old to register");
                break;

            case "age_too_old":
                // Different handling for maximum age
                flash.put("error", "Registration is limited to ages " &
                    metadata.maximumAllowed & " and under");
                break;

            default:
                // Fallback to standard error message
                flash.put("error", error.getMessage());
        }
    }
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="ValidationExample.cfm" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
// Validate and handle errors with metadata
var result = validate( target=user, constraints="userValidation" );

if ( result.hasErrors() ) {
    for ( var error in result.getAllErrors() ) {
        var metadata = error.getErrorMetadata();

        // Use metadata for enhanced error handling
        switch ( metadata.category ?: "" ) {
            case "email_duplicate":
                // Show specific message about existing user
                flash.put( "error", "This email was already registered on " &
                    dateFormat( metadata.existingSince, "mm/dd/yyyy" ) );
                break;

            case "age_too_young":
                // Age-specific guidance
                flash.put( "error", "You must be at least " &
                    metadata.minimumRequired & " years old to register" );
                break;

            case "age_too_old":
                // Different handling for maximum age
                flash.put( "error", "Registration is limited to ages " &
                    metadata.maximumAllowed & " and under" );
                break;

            default:
                // Fallback to standard error message
                flash.put( "error", error.getMessage() );
        }
    }
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

### Error Metadata in i18n Messages

Error metadata integrates seamlessly with CBValidation's i18n support. You can reference metadata values in your resource bundle messages:

{% code title="i18n/validation_en.properties" overflow="wrap" lineNumbers="true" %}

```properties
# Standard validation messages
email.required=Email address is required
age.required=Age is required

# Custom messages using error metadata
email.udf=The email address '{rejectedValue}' is already registered by user #{duplicateUserId}
age.method.too_young=You must be at least {minimumRequired} years old (you entered {provided})
age.method.too_old=Maximum age allowed is {maximumAllowed} (you entered {provided})
```

{% endcode %}

### ValidationError Metadata API

The `ValidationError` object provides methods to work with error metadata:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="ErrorMetadataAPI.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
// Get all metadata as a struct
var metadata = error.getErrorMetadata();

// Check if metadata exists
if (!structIsEmpty(metadata)) {
    // Access specific metadata values
    if (structKeyExists(metadata, "category")) {
        writeOutput("Error category: " & metadata.category);
    }

    // Get complete error information including metadata
    var errorInfo = error.getMemento();
    // errorInfo.errorMetadata contains all metadata
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="ErrorMetadataAPI.cfm" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
// Get all metadata as a struct
var metadata = error.getErrorMetadata();

// Check if metadata exists
if ( !structIsEmpty( metadata ) ) {
    // Access specific metadata values
    if ( structKeyExists( metadata, "category" ) ) {
        writeOutput( "Error category: " & metadata.category );
    }

    // Get complete error information including metadata
    var errorInfo = error.getMemento();
    // errorInfo.errorMetadata contains all metadata
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

### Best Practices for Error Metadata

1. **Use Consistent Structure**: Establish consistent metadata keys across your validators for easier handling
2. **Meaningful Categories**: Use category/type fields to enable different error handling logic
3. **Include Context**: Add relevant business context like IDs, dates, or limits that help with error resolution
4. **Avoid Sensitive Data**: Don't include passwords or other sensitive information in metadata
5. **Document Your Schema**: If building reusable validators, document what metadata fields they provide

{% hint style="info" %}
**Version Requirement**: Error metadata is available in CBValidation 4.3.0+. UDF and Method validators in earlier versions will receive only the `value` and `target` arguments.
{% endhint %}

{% hint style="success" %}
**Pro Tip**: Use error metadata to create more user-friendly error messages and implement sophisticated error handling logic without modifying core validation behavior.
{% endhint %}
