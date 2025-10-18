---
icon: sparkles
---

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
        type  : (alpha,array,binary,boolean,component,creditcard,date,email,float,GUID,integer,ipaddress,json,numeric,query,ssn,string,struct,telephone,url,usdate,UUID,xml,zipcode),

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

The field must be yes, on, 1, or true. This is useful for validating "Terms of Service" acceptance. _Note: This validator will ignore values that are null or empty strings._

```javascript
terms = { accepted : true }
```

**Common Use Cases:**

* Terms of service and privacy policy acceptance
* Newsletter subscription opt-ins
* Age verification checkboxes
* Legal disclaimer acknowledgments
* Cookie consent confirmations

## after

The field under validation must be a value after a given date. The dates will be passed into the `dateCompare()` function in order to be converted and tested. _Note: This validator will ignore values that are null or empty strings._

```javascript
startDate : { required:true, type:"date", after: dateAdd( "d", 1, now() ) }
```

Instead of passing a date, you may specify another field to compare against the date as well:

```javascript
endDate : { required:true, type:"date", after: "startDate" }
```

**Common Use Cases:**

* Event end dates must be after start dates
* Subscription expiration dates after purchase dates
* Delivery dates after order dates
* Meeting end times after start times
* Contract termination dates after effective dates

## afterOrEqual

The field under validation must be a value after or equal a given date. The dates will be passed into the `dateCompare()` function in order to be converted and tested. _Note: This validator will ignore values that are null or empty strings._

```javascript
startDate : { required:true, type:"date", afterOrEqual: dateAdd( "d", 1, now() ) }
```

## alpha

The field must be alphabetical ONLY. _Note: This validator will ignore values that are null or empty strings._

```javascript
firstName = { alpha = true }
```

**Common Use Cases:**

* First and last names (no numbers or special characters)
* Department or division names
* Country and city names
* Product category names
* Language or locale identifiers

## arrayItem

This validator is used to validate an array's items. It will iterate through each of the array's items and validate each item against the `validationData` constraints you pass in. _Note: This validator will ignore values that are null or empty strings._

```javascript
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

```javascript
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

```javascript
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

**Common Use Cases:**

* Shopping cart items validation
* Form field arrays (multiple phone numbers, addresses)
* Tag lists and category arrays
* File upload collections
* Multi-select option validation
* Invoice line items
* Survey question responses

## before

The field under validation must be a value before a given date. The dates will be passed into the `dateCompare()` function in order to be converted and tested. _Note: This validator will ignore values that are null or empty strings._

```javascript
endDate : { required:true, type:"date", before: "01/01/2022" }
```

Instead of passing a date, you may specify another field to compare against the date as well:

```javascript
startDate : { required:true, type:"date", before: "endDate" }
```

## beforeOrEqual

The field under validation must be a value before or equal a given date. The dates will be passed into the `dateCompare()` function in order to be converted and tested. _Note: This validator will ignore values that are null or empty strings._

```javascript
endDate : { required:true, type:"date", beforeOrEqual: "01/01/2022" }
```

## constraints

This validator is used to validate a nested struct. The value of this validator are the constraints for the nested struct. _Note: This validator will ignore values that are null._

```javascript
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

```javascript
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

```javascript
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

There is a [shortcut notation available](nested-struct-and-array-field-name-shortcuts.md#nested-struct-shorthand) for `constraints` that uses a specialized field name to skip nesting the constraints.

## dateEquals

The field under validation must be a value that is the same as the given date. The dates will be passed into the `dateCompare()` function in order to be converted and tested. _Note: This validator will ignore values that are null or empty strings._

```javascript
endDate : { required:true, type:"date", dateEquals: "01/01/2022" }
```

Instead of passing a date, you may specify another field to compare against the date as well:

```javascript
startDate : { required:true, type:"date", dateEquals: "createdDate" }
```

## discrete

The field must pass certain discrete math operations using the format: `operator:value` _Note: This validator will ignore values that are null or empty strings._

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

The field is not required but if it exists it cannot be empty. _Note: This validator will ignore values that are null._

```javascript
myField = { empty = false }
```

This is needed since [required](./#required) validators allow empty strings when `false` while [type](./#type) validators ignore empty values as valid. This means we can have a situation as follows:

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

The field must be in the included list. _Note: This validator will ignore values that are null or empty strings._

```javascript
status = { inList = "active,inactive,pending" },
priority = { inList = "low,medium,high,critical" },
color = { inList = "red,green,blue,yellow" }
```

**Common Use Cases:**

* Status fields (active/inactive, published/draft)
* Priority levels (low/medium/high/critical)
* User roles (admin/user/guest/moderator)
* Product categories or types
* Geographic regions or time zones
* Payment methods (credit/debit/paypal/stripe)
* File formats or MIME types

## instanceOf

The value passed must be an instance of a particular type. This validator checks that an object is an instance of a specific class or component, useful for validating that dependency injection worked correctly or that factory methods returned the expected type. _Note: This validator will ignore values that are null or empty strings._

```javascript
// Basic dependency injection validation
userService: {
    required: true,
    instanceOf: "UserService"  // Must be UserService instance
},
emailService: {
    instanceOf: "models.services.EmailService"  // Full path validation
},
configBean: {
    instanceOf: "ConfigurationBean"  // Validate configuration objects
}
```

**Advanced Usage Examples:**

```javascript
// Factory pattern validation
gateway: {
    required: true,
    instanceOf: "PaymentGateway"  // Ensure factory returned correct type
},
validator: {
    instanceOf: "CreditCardValidator"  // Type-safe validator injection
},

// API Response validation
data: { instanceOf: "models.ResultCollection" },
pagination: { instanceOf: "models.PaginationInfo" }
```

**Common Use Cases:**

* **Dependency Injection Validation**: Ensure WireBox injected the correct service types
* **Factory Pattern Validation**: Verify factory methods return expected object types
* **API Response Validation**: Validate that API responses contain properly typed objects
* **Plugin/Module Validation**: Ensure loaded plugins implement required interfaces
* **Configuration Validation**: Verify configuration objects are the expected type

**Path Specification:**

* Use simple names for objects in the same package: `"UserService"`
* Use dot notation for full paths: `"models.services.UserService"`
* Works with interfaces and abstract classes
* Supports CFC inheritance checking

## items

See [arrayItem](./#arrayitem).

## max

The field must be less than or equal to the defined value. _Note: This validator will ignore values that are null or empty strings._

```javascript
age = { max = 120 },
price = { max = 9999.99 },
quantity = { max = 100 }
```

**Common Use Cases:**

* Age limits and maximum age restrictions
* Price caps and budget limits
* Quantity restrictions in shopping carts
* File size limits (in MB/KB)
* Rating scales (1-5, 1-10)
* Percentage values (0-100)
* Inventory limits

## method

The `methodName` will be called on the target object and it will pass in validationData, targetValue, and metadata. It must return a boolean response: **true** = pass, **false** = fail.

Any data you place in the `metadata` structure will be set in the validation result object for later retrieval. _Note: This validator will ignore values that are null or empty strings._

```javascript
myField = { method = "methodName" }

function methodName( validationData, targetValue, metadata ){
    metadata[ "customMessage" ] = "I am a custom message set via metadata.";
    return false;
}
```

## min

The field must be greater than or equal to the defined value. _Note: This validator will ignore values that are null or empty strings._

```javascript
age = { min = 18 },
password = { min = 8 },
price = { min = 0.01 }
```

**Common Use Cases:**

* Minimum age requirements (18+, 21+)
* Password length requirements
* Minimum order values
* Required experience years
* Minimum bid amounts
* Rating thresholds
* Stock quantity minimums

## nestedConstraints

See [constraints](./#constraints).

## notSameAsNoCase

The field must NOT be the same as another field with no case sensitivity. This validator is useful for scenarios where you need to ensure two fields are different, regardless of letter casing. _Note: This validator will ignore values that are null or empty strings._

```javascript
// Password cannot be the same as username (case insensitive)
username: { required: true, size: "3..20" },
password: {
    required: true,
    size: "8..50",
    notSameAsNoCase: "username"  // Password can't match username
}
```

**Common Use Cases:**

* Preventing passwords from matching usernames
* Ensuring alternate contact fields are different
* Validating that backup values don't duplicate primary values

## notSameAs

The field must NOT be the same as another field with case sensitivity. This validator ensures exact case-sensitive comparison between fields. _Note: This validator will ignore values that are null or empty strings._

```javascript
// New password must be different from current password
currentPassword: { required: true },
newPassword: {
    required: true,
    size: "8..50",
    notSameAs: "currentPassword"  // Case-sensitive comparison
},
alternateEmail: {
    type: "email",
    notSameAs: "primaryEmail"  // Must be different emails
}
```

**Common Use Cases:**

* Password change validation (new password ≠ old password)
* Ensuring backup contact information is different
* Validating that case-sensitive codes or identifiers are unique

**When to Use Each:**

* Use `notSameAs` when case matters (passwords, case-sensitive codes)
* Use `notSameAsNoCase` when case doesn't matter (usernames, display names)

## range

The field must be within the range values and the validation data must follow the range pattern: `min..max`. _Note: This validator will ignore values that are null or empty strings._

```javascript
rating = { range = "1..5" },
temperature = { range = "-20..50" },
percentage = { range = "0..100" }
```

**Common Use Cases:**

* Rating systems (1-5 stars, 1-10 scale)
* Temperature ranges for equipment
* Percentage values (0-100%)
* Age ranges for demographics
* Price ranges for budgets
* Quantity ranges for bulk orders
* Time ranges (hours: 0-23, minutes: 0-59)

## regex

The field must pass the regular expression match with no case sensitivity. _Note: This validator will ignore values that are null or empty strings._

```javascript
leaveType = { regex = "^(sick|vacation|disability)$" },
productCode = { regex = "^[A-Z]{2}\d{4}$" },
phoneFormat = { regex = "^\(\d{3}\) \d{3}-\d{4}$" }
```

**Common Use Cases:**

* Product codes and SKU patterns
* Phone number formatting
* License plate formats
* Social security number patterns
* Custom ID formats (employee IDs, customer codes)
* URL slug patterns
* Version number formats

## required

The field must have some type of value and not null or an empty string.

```javascript
firstName = { required = true },
email = { required = true },
newsletter = { required = false }
```

**Common Use Cases:**

* Essential user information (name, email, password)
* Legal requirements (terms acceptance, age verification)
* Contact information for orders
* Mandatory form fields
* Required configuration settings
* Critical system parameters

## requiredIf

The field under validation must be present and not empty if the `anotherfield` field is equal to the passed `value`. The validation data can be a `struct` or a `string` representing the field to check, or it can be a UDF/closure/lambda to use for validation. The UDF must return **boolean**, `validate( value, target, metadata ):boolean`

Any data you place in the `metadata` structure will be set in the validation result object for later retrieval.

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

// UDF Based
myField = {
 // myField is required if today is monday.
 requiredIf = function( value, target, errorMetadata ) {
        return dayOfWeekAsString( dayOfWeek( now() ) ) == "Monday";
 }
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

The field must be the same as another field with no case sensitivity. _Note: This validator will ignore values that are null or empty strings._

```javascript
confirmEmail = { sameAsNoCase = "email" },
displayName = { sameAsNoCase = "username" }
```

**Common Use Cases:**

* Email confirmation (case-insensitive matching)
* Username verification fields
* Display name matching
* Case-insensitive code confirmation

## sameAs

The field must be the same as another field with case sensitivity. _Note: This validator will ignore values that are null or empty strings._

```javascript
confirmPassword = { sameAs = "password" },
verifyApiKey = { sameAs = "apiKey" }
```

**Common Use Cases:**

* Password confirmation fields
* API key verification
* Security code confirmation
* Case-sensitive token matching
* Exact duplicate field validation

## size

The field value size must be within the range values and the validation data must follow the range pattern: `min..max.` Value can be a (struct,string,array,query). _Note: This validator will ignore values that are null or empty strings._

```javascript
username = { size = "3..20" },
description = { size = "10..500" },
tags = { size = "1..10" }  // Array size
```

**Common Use Cases:**

* Username length requirements (3-20 characters)
* Password complexity (8-128 characters)
* Description fields (min/max word counts)
* Tag or category limits (max 10 items)
* Comment length restrictions
* File name length limits
* Array size validation (shopping cart items)

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

_Note: This validator will ignore values that are null or empty strings._

```javascript
email = { type = "email" },
price = { type = "numeric" },
birthDate = { type = "date" },
isActive = { type = "boolean" },
config = { type = "json" }
```

**Common Use Cases:**

* **email**: User registration, contact forms
* **numeric/float**: Prices, quantities, measurements
* **date/usdate**: Birth dates, appointment scheduling
* **boolean**: Feature toggles, yes/no questions
* **creditcard**: Payment processing
* **telephone**: Contact information
* **url**: Website links, API endpoints
* **json/xml**: Configuration data, API payloads
* **array/struct**: Complex data validation
* **guid/uuid**: Unique identifiers

## udf

The field value, the target object, and an empty metadata structure will be passed to the declared closure/lambda to use for validation. The UDF must return **boolean**, `validate( value, target, metadata ):boolean`

Any data you place in the `metadata` structure will be set in the validation result object for later retrieval. _Note: This validator will ignore values that are null or empty strings._

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

_Note: This validator will ignore values that are null or empty strings._

```javascript
username = { unique = { table = "users", column = "username" } },
email = { unique = { table = "users", column = "email_address" } },
productSku = { unique = { table = "products" } }  // Uses field name as column
```

**Common Use Cases:**

* User registration (unique usernames, emails)
* Product catalogs (unique SKUs, product codes)
* Employee records (unique employee IDs, SSNs)
* Customer accounts (unique account numbers)
* Inventory management (unique serial numbers)
* Content management (unique slugs, URLs)
* Organization data (unique department codes)

## validator

The field value will be passed to the validator CFC to be used for validation. Please see [Custom Validators](../../advanced/advanced-custom-validators.md)

```javascript
myField = { validator = "UniqueValidator@cborm" }
```
