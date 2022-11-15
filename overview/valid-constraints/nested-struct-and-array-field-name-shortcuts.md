# Nested Struct and Array Field Name Shortcuts

Defining nested struct or array item validation can create very nested code. cbvalidation allows for a shortcut to define these structures using a custom field name instead.

## Nested Struct Shorthand

For a nested struct, this is done by defining the field as a dot-delimited field name following the nested structure.

```cfscript
var validationResult = validate(
    target = {
        "address": {
            "streetOne" : "123 Elm Street",
            "streetTwo" : "",
            "city"      : "Anytown",
            "state"     : "IL",
            "zip"       : "60606"
        }
    },
    constraints = {
        "address": { "required": true, "type": "struct" },
        "address.streetOne": { "required": true, "type": "string" },
        "address.streetTwo": { "required": false, "type": "string" },
        "address.city": { "required": true, "type": "string" },
        "address.state": { "required": true, "type": "string", "size": 2 },
        "address.zip": { "required": true, "type": "numeric", "size": 5 }
    }
);
```

This can be continued as many levels deep as necessary.

```cfscript
var validationResult = validate(
    target = {
        "owner": {
            "firstName": "John",
            "lastName": "Doe",
            "address": {
                "streetOne" : "123 Elm Street",
                "streetTwo" : "",
                "city"      : "Anytown",
                "state"     : "IL",
                "zip"       : "60606"
            }
        }
    },
    constraints = {
        "owner.firstName": { "required": true, "type": "string" },
        "owner.lastName": { "required": true, "type": "string" },
        "owner.address.streetOne": { "required": true, "type": "string" },
        "owner.address.streetTwo": { "required": false, "type": "string" },
        "owner.address.city": { "required": true, "type": "string" },
        "owner.address.state": { "required": true, "type": "string", "size": 2 },
        "owner.address.zip": { "required": true, "type": "numeric", "size": 5 }
    }
);
```

## Nested Array Shorthand

For a nested array, this is done by defining the field as a dot-delimited field name following the nested structure using an asterisk (`*`) to represent the items of the array.

```cfscript
var validationResult = validate(
    target = {
        "luckyNumbers": [ 7, 11, 21 ]
    },
    constraints = {
        "luckyNumbers.*": { "type": "numeric" }
    }
);
```

The struct and array shorthand can be combined, as well.

```cfscript
var validationResult = validate(
    target = {
        "owner": {
            "firstName": "John",
            "lastName": "Doe",
            "addresses": [
                {
                    "streetOne" : "123 Elm Street",
                    "streetTwo" : "",
                    "city"      : "Anytown",
                    "state"     : "IL",
                    "zip"       : "60606"
                }
            ]
        }
    },
    constraints = {
        "owner.firstName": { "required": true, "type": "string" },
        "owner.lastName": { "required": true, "type": "string" },
        "owner.addresses.*.streetOne": { "required": true, "type": "string" },
        "owner.addresses.*.streetTwo": { "required": false, "type": "string" },
        "owner.addresses.*.city": { "required": true, "type": "string" },
        "owner.addresses.*.state": { "required": true, "type": "string", "size": 2 },
        "owner.addresses.*.zip": { "required": true, "type": "numeric", "size": 5 }
    }
);
```
