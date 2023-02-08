# Available Constraints

Below are all the currently supported constraints. If you need more you can create your own [Custom validators](../../advanced/advanced-custom-validators.md) as well.

```javascript
propertyName = {
        // The field under validation must be yes, on, 1, or true. This is useful for validating "Terms of Service" acceptance.
        accepted : any value
        
        // The field under validation must be a date after the set targetDate
        after : targetDate
        
        // The field under validation must be a date after or equal the set targetDate
        afterOrEqual : targetDate

        // The field must be alpha ONLY
        alpha : any value
        
        // The field under validation is an array and all items must pass this validation as well
        arrayItem : {
            // All the constraints to validate the items with
        }
        
        // The field under validation must be a date before the set targetDate
        before : targetDate
        
        // The field under validation must be a date before or equal the set targetDate
        beforeOrEqual : targetDate
        
        // The field under validation is a struct and all nested validation rules must pass
        constraints: {
           // All the constraints for the nested struct
        }
        
        // The field under validation must be a date that is equal the set targetDate
        dateEquals : targetDate
        
        // discrete math modifiers
        discrete : (gt,gte,lt,lte,eq,neq):value
        
        // the field must or must not be an empty value
        // needed because `required` counts empty strings as valid
        // and `type` ignores empty strings as "not required"
        empty : boolean [false]

        // value in list
        inList : list
        
        // Verify the instance of the target
        InstanceOf : "instance.path"
        
        // An alias for arrayItem
        items : {
            // All the constraints to validate the items with
        }

        // max value
        max : value

        // Validation method to use in the target object must return boolean accept the incoming value and target object 
        method : methodName

        // min value
        min : value
        
        // An alias for constraints
        nestedConstraints: {
           // All the constraints for the nested struct
        }
        
        // not same as but with no case
        notSameAsNoCase : propertyName

        // not same as another property
        notSameAs : propertyName

        // range is a range of values the property value should exist in
        range : eg: 1..10 or 5..-5

        // regex validation
        regex : valid no case regex

        // required field or not, includes null values
        required : boolean [false]

        // The field under validation must be present and not empty if the `anotherfield` field is equal to the passed `value`.
        requiredIf : {
            anotherfield:value, anotherfield:value
        }

        // The field under validation must be present and not empty unless the `anotherfield` field is equal to the passed 
        requiredUnless : {
            anotherfield:value, anotherfield:value
        }

        // same as but with no case
        sameAsNoCase : propertyName

        // same as another property
        sameAs : propertyName

        // size or length of the value which can be a (struct,string,array,query)
        size  : numeric or range, eg: 10 or 6..8

        // specific type constraint, one in the list.
        type  : (alpha,array,binary,boolean,component,creditcard,date,email,eurodate,float,GUID,integer,ipaddress,json,numeric,query,ssn,string,struct,telephone,url,usdate,UUID,xml,zipcode),

        // UDF to use for validation, must return boolean accept the incoming value and target object, validate(value,target,metadata):boolean
        udf = variables.UDF or this.UDF or a closure.

        // Check if a column is unique in the database
        unique = {
            table : The table name,
            column : The column to check, defaults to the property field in check
        }

        // Custom validator, must implement coldbox.system.validation.validators.IValidator
        validator : path or wirebox id, example: 'mypath.MyValidator' or 'id:MyValidator'
}
```

## accepted

The field must be yes, on, 1, or true. This is useful for validating "Terms of Service" acceptance.

```javascript
terms = { accepted = true }
```

## after

The field under validation must be a value after a given date. The dates will be passed into the `dateCompare()` function in order to be converted and tested.

```javascript
startDate : { required:true, type:"date", after: dateAdd( "d", 1, now() ) }
```

Instead of passing a date, you may specify another field to compare against the date as well:

```javascript
endDate : { required:true, type:"date", after: "startDate" }
```

## afterOrEqual

The field under validation must be a value after or equal a given date. The dates will be passed into the `dateCompare()` function in order to be converted and tested.

```javascript
startDate : { required:true, type:"date", afterOrEqual: dateAdd( "d", 1, now() ) }
```

## alpha

The field must be alphabetical ONLY

```javascript
terms = { alpha = true }
```

## arrayItem

This validator is used to validate an array's items. It will iterate through each of the array's items and validate each item against the `validationData` constraints you pass in.

```cfscript
luckyNumbers = {
    required : true,
    type : "array",
    arrayItem : {
        required : true,
        type : "numeric"
    }
}
```

You may also specify `items` as an alias to `arrayItem`.

```cfscript
luckyNumbers = {
    required : true,
    type : "array",
    items : {
        required : true,
        type : "numeric"
    }
}
```

Any validation errors found will be named using the parent field name and array index.

```cfscript
var validationResult = validate(
    target = {
        "luckyNumbers": [ 7, 11, "not a number", 21 ]
    },
    constraints = {
        required : true,
        type : "array",
        items : {
            required : true,
            type : "numeric"
        }
    }
);
```

```json
// validationResult.getAllErrorsAsJson()
{
    "luckyNumbers[3]": ["The 'item' has an invalid type, expected type is numeric"]
}
```

You can validate nested structs by nesting a `constraints` validator.

```javascript
invoiceItems = {
    required : true,
    type : "array",
    arrayItem : {
        type : "struct",
        constraints : {
            logDate : { required : true, type : "date" },
            isBilled : { required: true, type : "boolean" },
            notes : { required: true }
        }
    }
}
```

There is a [shortcut notation available](nested-struct-and-array-field-name-shortcuts.md#nested-array-shorthand) for `arrayItem` that uses a specialized field name to skip nesting the constraints.

## before

The field under validation must be a value before a given date. The dates will be passed into the `dateCompare()` function in order to be converted and tested.

```javascript
endDate : { required:true, type:"date", before: "01/01/2022" }
```

Instead of passing a date, you may specify another field to compare against the date as well:

```javascript
startDate : { required:true, type:"date", before: "endDate" }
```

## beforeOrEqual

The field under validation must be a value before or equal a given date. The dates will be passed into the `dateCompare()` function in order to be converted and tested.

```javascript
endDate : { required:true, type:"date", beforeOrEqual: "01/01/2022" }
```

## constraints

This validator is used to validate a nested struct. The value of this validator are the constraints for the nested struct.

```cfscript
address = {
    "required": true,
    "type": "struct",
    "constraints": {
        "streetOne": { "required": true, "type": "string" },
        "streetTwo": { "required": false, "type": "string" },
        "city": { "required": true, "type": "string" },
        "state": { "required": true, "type": "string", "size": 2 },
        "zip": { "required": true, "type": "numeric", "size": 5 }
    }
}
```

Any validation errors found will be named using the parent field name and the child field name.

```cfscript
var validationResult = validate(
    target = {
        "address": {
            "streetOne" : "123 Elm Street",
            "streetTwo" : "",
            "city"      : "Anytown",
            "zip"       : "60606"
        }
    },
    constraints = {
        "address": {
            "required": true,
            "type": "struct",
            "constraints": {
                "streetOne": { "required": true, "type": "string" },
                "streetTwo": { "required": false, "type": "string" },
                "city": { "required": true, "type": "string" },
                "state": { "required": true, "type": "string", "size": 2 },
                "zip": { "required": true, "type": "numeric", "size": 5 }
            }
        }
    }
);
```

```json
// validationResult.getAllErrorsAsJson()
{
    "address.state": ["The 'state' field is required"]
}
```

`constraints` can be used as many levels deep as you need to go.

```cfscript
owner = {
    "firstName": { "required": true, "type": "string" },
    "lastName": { "required": true, "type": "string" },
    "address": {
        "required": true,
        "type": "struct",
        "constraints": {
            "streetOne": { "required": true, "type": "string" },
            "streetTwo": { "required": false, "type": "string" },
            "city": { "required": true, "type": "string" },
            "state": { "required": true, "type": "string", "size": 2 },
            "zip": { "required": true, "type": "numeric", "size": 5 }
        }
    }
}
```

`constraints` can also be combined with `items` to validate an array of structs.

```cfscript
invoiceItems = {
    required : true,
    type : "array",
    arrayItem : {
        type : "struct",
        constraints : {
            logDate : { required : true, type : "date" },
            isBilled : { required: true, type : "boolean" },
            notes : { required: true }
        }
    }
}
```

There is a [shortcut notation available](nested-struct-and-array-field-name-shortcuts.md#nested-struct-shorthand) for `constraints` that uses a specialized field name to skip nesting the constraints.

## dateEquals

The field under validation must be a value that is the same as the given date. The dates will be passed into the `dateCompare()` function in order to be converted and tested.

```javascript
endDate : { required:true, type:"date", dateEquals: "01/01/2022" }
```

Instead of passing a date, you may specify another field to compare against the date as well:

```javascript
startDate : { required:true, type:"date", dateEquals: "createdDate" }
```

## discrete

The field must pass certain discrete math operations using the format: `operator:value`

* `gt` - Greater than the value
* `gte` - Greater than or equal to the value
* `lt` - Less than the value
* `lte` - Less than or equal to the value
* `eq` - Equal to the value
* `neq` - Not equal to the value

```javascript
myField = { discrete = "gt:4" }
myField = { discrete = "eq:luis" }
myField = { discrete = "lte:1" }
```

## empty

The field is not required but if it exists it cannot be empty.

```javascript
myField = { empty = false }
```

This is needed since [required](./#required) validators allow empty strings when `false` while [type](./#type) validators ignore empty values as valid.  This means we can have a situation as follows:

```javascript
{
    "startDate": {
        "required": false,
        "type": "date"
    }
}
```

With these validation rules passing in `startDate = ""` would pass the validation! The empty validator helps us ensure that the value passed in is not empty (and, in this case, a date).

```javascript
{
    "startDate": {
        "required": false,
        "empty": false,
        "type": "date"
    }
}
```

The field still isn't required, but if it is passed the value must be a non-empty value and it must be parseable as a date.

## inList

The field must be in the included list

```javascript
myField = { inList = "red,green,blue" }
```

## items

See [arrayItem](./#arrayitem).

## max

The field must be less than or equal to the defined value

```javascript
myField = { max = 25 }
```

## method

The `methodName` will be called on the target object and it will pass in validationData, targetValue, and metadata. It must return a boolean response: **true** = pass, **false** = fail.

Any data you place in the `metadata` structure will be set in the validation result object for later retrieval.

```javascript
myField = { method = "methodName" }

function methodName( validationData, targetValue, metadata ){
    metadata[ "customMessage" ] = "I am a custom message set via metadata.";
    return false;
}
```

## min

The field must be greater than or equal to the defined value

```javascript
myField = { min = 8 }
```

## nestedConstraints

See [constraints](./#constraints).



## notSameAsNoCase

The field must NOT be the same as another field with no case sensitivity

```javascript
myField = { notSameAsNoCase = "otherField" }
```

## notSameAs

The field must NOT be the same as another field with case sensitivity

```javascript
myField = { notSameAs = "otherField" }
```

## range

The field must be within the range values and the validation data must follow the range pattern: `min..max`

```javascript
myField = { range = "1..5" }
myField = { range = "5..-5" }
```

## regex

The field must pass the regular expression match with no case sensitivity

```javascript
myField = { regex = "^(sick|vacation|disability)$" }
```

## required

The field must have some type of value and not null.

```javascript
myField = { required=true }
myField = { required=false }
```

## requiredIf

The field under validation must be present and not empty if the `anotherfield` field is equal to the passed `value`. The validation data can be a `struct` or a `string` representing the field to check.

```javascript
// Struct based
myField = { 
 // myField is required if field2 = test and field3 = hello
 requiredIf = {
  field2 = "test",
  field3 = "hello"
 }
}

// String Based
myField = {
 // myField is required if field3 exists and has a value.
 requiredIf = "field3"
}
```

## requiredUnless

The field under validation must be present and not empty unless the `anotherfield` field is equal to the passed `value`. The validation data can be a `struct` or a `string` representing the field to check.

```javascript
myField = { 
 // myField is required unless field2 = test and field3 = hello
 requiredUnless = {
  field2 = "test",
  field3 = "hello"
 }
}

// String Based
myField = {
 // myField is required unless field3 exists and has a value.
 requiredUnless = "field3"
}
```

## sameAsNoCase

The field must be the same as another field with no case sensitivity

```javascript
myField = { sameAsNoCase = "otherField" }
```

## sameAs

The field must be the same as another field with case sensitivity

```javascript
myField = { sameAs = "otherField" }
```

## size

The field value size must be within the range values and the validation data must follow the range pattern: `min..max.` Value can be a (struct,string,array,query)

```javascript
myField = { size : 10 }
myFiedl = { size : "8..20" }
```

## type

One of the most versatile validators. It can test if the value is of the following specific types:

* alpha
* array
* binary
* boolean
* component
* creditcard
* date
* email
* eurodate
* float
* GUID
* integer
* ipaddress
* json
* numeric
* query
* ssn
* string
* struct
* telephone
* url
* usdate
* UUID
* xml
* zipcode

```javascript
myField = { type : "float" }
myField = { type : "json" }
myField = { type : "xml" }
```

## udf

The field value, the target object, and an empty metadata structure will be passed to the declared closure/lambda to use for validation. The UDF must return **boolean**, `validate( value, target, metadata ):boolean`

Any data you place in the `metadata` structure will be set in the validation result object for later retrieval.

```javascript
myField = { udf = function( value, target, metadata ) { return true; } }
myField = { udf = (value ,target, metadata ) => true }
myField = { udf = function( value, target, metadata ) { 
    metadata[ "customMessage" ] = "This is a custom error message from within the udf";
    return false; 
}
```

## unique

The field must be a unique value in a specific database table. The validation data is a struct with the following keys:

* `table` : The name of the table to check
* `column` : The column to check, defaults to the property field in check

```javascript
myField = { unique = { table : "users", column : "username" } }
```

## validator

The field value will be passed to the validator CFC to be used for validation. Please see [Custom Validators](../../advanced/advanced-custom-validators.md)

```javascript
myField = { validator = "UniqueValidator@cborm" }
```
