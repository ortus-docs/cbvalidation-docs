---
description: 2022-JAN-12
---

# What's New With 3.3.0

### Nested Constraints

Nested structs can be validated using the `constraints` or `nestedConstraints` validator.

```javascript
validateOrFail(
    target = {
        "owner": { "firstName": "John", "lastName": "Doe" }
    },
    constraints = {
        "owner": {
            "constraints": {
                "firstName": { "required": true, "type": "string" },
                "lastName": { "required": true, "type": "string" },
            }
        }
    }
);
```

Using the `nestedConstraints` validator requires the item it is used on be a struct. Otherwise a validation error will occur.

\
When using `nestedConstraints` the field name of the errors will be the dot-delimited path of the target.

```javascript
validateOrFail(
    target = {
        "owner": { "firstName": "John" }
    },
    constraints = {
        "owner": {
            "constraints": {
                "firstName": { "required": true, "type": "string" },
                "lastName": { "required": true, "type": "string" },
            }
        }
    }
);

// ValidationError -> { 
    field: "owner.lastName", 
    message: "The `lastName` field is required" 
}
```

The field name change also applies to `items` or `arrayItem` validators.

```javascript
validateOrFail(
    target = {
        "luckyNumbers": [ 7, "not a number", 11 ]
    },
    constraints = {
        "luckyNumbers": {
            "items": {
                "required": true,
                "type": "numeric"
            }
        }
    }
);

// ValidationError -> { 
field: "luckyNumbers[2]", 
message: "The 'item' has an invalid type, expected type is numeric" 
}
```

The field name changes will allow you to match the validation errors to your fields in your forms.\
While **cbValidation** can nest constraints down as far as you'd like to go, remember that each nested level increases complexity.

### Array and Struct Shorthand Syntax

To make defining array item and nested constraint validators easier, you can use a shorthand on the field name, like so:

```javascript
validateOrFail(
    target = {
        "owner": {
            "firstName": "John",
            "lastName": "Doe",
            "luckyNumbers": [ 7, 11, 21 ],
            "addresses": [
                {
                    "streetOne": "123 Elm Street",
                    "city": "Anytown",
                    "state": "IL",
                    "zip": 60606
                }
            ]
        }
    },
    constraints = {
        "owner.firstName": { "required": true, "type": "string" },
        "owner.lastName": { "required": true, "type": "string" },
        "owner.luckyNumbers.*": { "required": true, "type": "numeric" },
        "owner.addresses.*.streetOne": { "required": true, "type": "string" },
        "owner.addresses.*.streetTwo": { "required": false, "type": "string" },
        "owner.addresses.*.city": { "required": true, "type": "string" },
        "owner.addresses.*.state": { "required": true, "type": "string", "size": 2 },
        "owner.addresses.*.zip": { "required": true, "type": "numeric", "size": 5 }
    }
);
```

Dot-delimited strings represent nested structs while the asterisk (`*`) represents an array of items. This shorthand syntax is expanded to the equivalent syntax above before validating. Use whichever you prefer.

### Validator Aliases

A few of the built-in validators now have an alias in addition to the long form validator name:

```javascript
{
    "items": "arrayItem",
    "constraints": "nestedConstraints"
}
```

Co-authored by [@garciadev](https://github.com/garciadev)

### Added

* Allow UDF and Method Validators to Utilize Error Metadata by @homestar9 ([https://github.com/coldbox-modules/cbvalidation/pull/48](https://github.com/coldbox-modules/cbvalidation/pull/48))
* Validator Aliases
* Array and Struct Shorthand Syntax
* Nested Constraints

### Fixed

* Date Comparisons Fail if Compare field is empty #58 thanks to @nockhigan: [https://github.com/coldbox-modules/cbvalidation/pull/58](https://github.com/coldbox-modules/cbvalidation/pull/58)
