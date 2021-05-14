# Available Constraints

Below are all the currently supported constraints. If you need more you can create your own [Custom validators](../../advanced/advanced-custom-validators.md) as well.

```javascript
propertyName = {
        // The field under validation must be yes, on, 1, or true. This is useful for validating "Terms of Service" acceptance.
        accepted : any value,

        // The field must be alphanumeric ONLY
        alpha : any value,

        // discrete math modifiers
        discrete : (gt,gte,lt,lte,eq,neq):value

        // value in list
        inList : list,

        // max value
        max : value,

        // Validation method to use in the target object must return boolean accept the incoming value and target object 
        method : methodName,

        // min value
        min : value,

        // range is a range of values the property value should exist in
        range : eg: 1..10 or 5..-5,

        // regex validation
        regex : valid no case regex

        // required field or not, includes null values
        required : boolean [false],

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

        // UDF to use for validation, must return boolean accept the incoming value and target object, validate(value,target):boolean
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

## alpha

The field must be alphabetical ONLY

```javascript
terms = { alpha = true }
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

## inList

The field must be in the included list

```javascript
myField = { inList = "red,green,blue" }
```

## max

The field must be less than or equal to the defined value

```javascript
myField = { max = 25 }
```

## method

The `methodName` will be called on the target object and it will pass in validationData and targetValue. It must return a boolean response: **true** = pass, **false** = fail.

```javascript
myField = { method = "methodName" }

function methodName( validationData, targetValue ){
    return true;
}
```

## min

The field must be greater than or equal to the defined value

```javascript
myField = { min = 8 }
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
myField = { sameAs = "otherField" }
```

## sameAs

The field must be the same as another field with case sensitivity

```javascript
myField = { sameAs = "otherField" }
```

## size

The field value size must be within the range values and the validation data must follow the range pattern: `min..max.` Value can be a \(struct,string,array,query\)

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
```

## udf

The field value will be passed to the declared closure/lambda to use for validation, must return **boolean** accept the incoming value and target object, `validate(value,target):boolean`

```javascript
myField = { udf = function( value, target ) { return true; } }
myField = { udf = (value,target) => true }
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

