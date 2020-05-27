# Custom Validators

If the core validators are not sufficient for you, then you can create your own custom validators. You can either leverage the `udf` validator and create your own closure/lambda to validate inline or create a reusable validator CFC

## Closure/Lambda Validator

If you use the `udf` validator, then you can declare your validation inline. Just create a closure/lambda that will be called for you at the time of validation. This closure/lambda will receive all the following arguments and MUST return a boolean indicator: **true** =&gt; passed, **false** =&gt; invalid

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
If you don't have any validation data to pass to a validator, just pass an empty struct \(`{}`\) or an empty string
{% endhint %}

