# Nested Struct and Array Field Name Shortcuts

CBValidation provides powerful shortcuts for validating nested data structures without requiring complex constraint definitions. Using dot notation for structs and asterisk (`*`) notation for arrays, you can validate deeply nested objects with clean, readable constraint definitions.

## Why Use Field Name Shortcuts?

Traditional nested validation would require defining complex constraint structures that mirror your data. Field name shortcuts allow you to:

- **Simplify constraint definitions** - Use flat, dot-notation paths instead of nested structures
- **Improve readability** - Clear field paths like `user.profile.email` are self-documenting
- **Reduce complexity** - Avoid deeply nested constraint objects
- **Handle dynamic structures** - Validate arrays of unknown length with `*` notation

## Nested Struct Shorthand

For a nested struct, this is done by defining the field as a dot-delimited field name following the nested structure.

```groovy
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

```groovy
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

## Array Validation Shortcuts

### Simple Array Validation

Use the asterisk (`*`) notation to validate all items in an array:

```groovy
// Validate array of numbers
var validationResult = validate(
    target = {
        "luckyNumbers": [ 7, 11, 21, 42 ],
        "favoriteColors": [ "red", "blue", "green" ],
        "scores": [ 85, 92, 78, 96 ]
    },
    constraints = {
        "luckyNumbers.*": { "type": "numeric", "range": "1..100" },
        "favoriteColors.*": { "type": "string", "size": "3..20" },
        "scores.*": { "type": "numeric", "range": "0..100" }
    }
);
```

### Array of Structs

Validate objects within arrays by combining dot notation with asterisk:

```groovy
var validationResult = validate(
    target = {
        "employees": [
            { "name": "John Doe", "email": "john@example.com", "age": 30 },
            { "name": "Jane Smith", "email": "jane@example.com", "age": 25 }
        ]
    },
    constraints = {
        "employees.*.name": { "required": true, "type": "string", "size": "2..50" },
        "employees.*.email": { "required": true, "type": "email" },
        "employees.*.age": { "required": true, "type": "numeric", "range": "18..65" }
    }
);
```

### Complex Nested Arrays

Handle deeply nested structures with multiple array levels:

```groovy
var validationResult = validate(
    target = {
        "departments": [
            {
                "name": "Engineering",
                "teams": [
                    {
                        "name": "Backend",
                        "members": ["John", "Jane", "Bob"]
                    },
                    {
                        "name": "Frontend",
                        "members": ["Alice", "Charlie"]
                    }
                ]
            }
        ]
    },
    constraints = {
        "departments.*.name": { "required": true, "type": "string" },
        "departments.*.teams.*.name": { "required": true, "type": "string" },
        "departments.*.teams.*.members.*": { "type": "string", "size": "2..30" }
    }
);
```

## Combined Struct and Array Examples

### User Profile with Multiple Addresses

```groovy
var validationResult = validate(
    target = {
        "user": {
            "profile": {
                "firstName": "John",
                "lastName": "Doe",
                "email": "john@example.com"
            },
            "addresses": [
                {
                    "type": "home",
                    "streetOne": "123 Elm Street",
                    "city": "Anytown",
                    "state": "IL",
                    "zip": "60606"
                },
                {
                    "type": "work",
                    "streetOne": "456 Oak Avenue",
                    "city": "Business City",
                    "state": "CA",
                    "zip": "90210"
                }
            ],
            "phoneNumbers": [
                { "type": "mobile", "number": "555-123-4567" },
                { "type": "home", "number": "555-987-6543" }
            ]
        }
    },
    constraints = {
        // Profile validation
        "user.profile.firstName": { "required": true, "type": "string", "size": "2..50" },
        "user.profile.lastName": { "required": true, "type": "string", "size": "2..50" },
        "user.profile.email": { "required": true, "type": "email" },

        // Address validation
        "user.addresses.*.type": { "required": true, "inList": "home,work,other" },
        "user.addresses.*.streetOne": { "required": true, "type": "string", "size": "5..100" },
        "user.addresses.*.city": { "required": true, "type": "string", "size": "2..50" },
        "user.addresses.*.state": { "required": true, "type": "string", "size": "2" },
        "user.addresses.*.zip": { "required": true, "regex": "^\d{5}(-\d{4})?$" },

        // Phone number validation
        "user.phoneNumbers.*.type": { "required": true, "inList": "mobile,home,work" },
        "user.phoneNumbers.*.number": { "required": true, "regex": "^\d{3}-\d{3}-\d{4}$" }
    }
);
```

### E-commerce Order Structure

```groovy
var validationResult = validate(
    target = {
        "order": {
            "customer": {
                "name": "John Doe",
                "email": "john@example.com"
            },
            "items": [
                {
                    "productId": "PROD123",
                    "name": "Widget",
                    "quantity": 2,
                    "price": 19.99
                },
                {
                    "productId": "PROD456",
                    "name": "Gadget",
                    "quantity": 1,
                    "price": 49.99
                }
            ],
            "shipping": {
                "method": "standard",
                "address": {
                    "street": "123 Main St",
                    "city": "Anytown",
                    "zip": "12345"
                }
            }
        }
    },
    constraints = {
        // Customer validation
        "order.customer.name": { "required": true, "type": "string", "size": "2..100" },
        "order.customer.email": { "required": true, "type": "email" },

        // Order items validation
        "order.items.*.productId": { "required": true, "type": "string", "regex": "^PROD\d+$" },
        "order.items.*.name": { "required": true, "type": "string", "size": "1..200" },
        "order.items.*.quantity": { "required": true, "type": "numeric", "min": "1" },
        "order.items.*.price": { "required": true, "type": "numeric", "min": "0.01" },

        // Shipping validation
        "order.shipping.method": { "required": true, "inList": "standard,express,overnight" },
        "order.shipping.address.street": { "required": true, "type": "string" },
        "order.shipping.address.city": { "required": true, "type": "string" },
        "order.shipping.address.zip": { "required": true, "regex": "^\d{5}$" }
    }
);
```

## Error Message Context

When validation fails on nested fields, the error messages include the full field path for precise error identification:

```groovy
var result = validate(target=orderData, constraints=orderConstraints);

if (result.hasErrors()) {
    for (var error in result.getAllErrors()) {
        // Examples of nested error fields:
        // "user.profile.email" - Invalid email format
        // "user.addresses.0.zip" - Invalid zip code (first address)
        // "order.items.1.price" - Price must be positive (second item)
        writeOutput("Field: #error.getField()# - #error.getMessage()#<br>");
    }
}
```

## Best Practices

### 1. Use Descriptive Field Names

```groovy
// ❌ Hard to understand
"data.items.*.val": { "required": true }

// ✅ Clear and descriptive
"products.items.*.price": { "required": true, "type": "numeric", "min": "0.01" }
```

### 2. Group Related Constraints

```groovy
// ❌ Mixed up constraint definitions
constraints = {
    "user.email": { "required": true, "type": "email" },
    "user.addresses.*.zip": { "required": true },
    "user.name": { "required": true },
    "user.addresses.*.city": { "required": true }
};

// ✅ Logically grouped
constraints = {
    // User basic info
    "user.name": { "required": true, "type": "string" },
    "user.email": { "required": true, "type": "email" },

    // Address validation
    "user.addresses.*.city": { "required": true, "type": "string" },
    "user.addresses.*.zip": { "required": true, "regex": "^\d{5}$" }
};
```

### 3. Validate Array Container and Items

```groovy
constraints = {
    // Validate the array itself
    "user.addresses": { "required": true, "type": "array", "size": "1..5" },

    // Validate array items
    "user.addresses.*.street": { "required": true, "type": "string" },
    "user.addresses.*.city": { "required": true, "type": "string" }
};
```

## Common Use Cases

### API Request Validation

Perfect for validating complex JSON payloads:

```groovy
// POST /api/users - Create user with profile and preferences
var apiConstraints = {
    "name": { "required": true, "type": "string", "size": "2..100" },
    "email": { "required": true, "type": "email" },
    "profile.bio": { "type": "string", "size": "0..500" },
    "profile.socialLinks.*.platform": { "inList": "twitter,linkedin,github" },
    "profile.socialLinks.*.url": { "type": "url" },
    "preferences.notifications.*.type": { "required": true },
    "preferences.notifications.*.enabled": { "type": "boolean" }
};
```

### Form Data Processing

Handle complex form submissions with nested data:

```groovy
// Registration form with emergency contacts
var formConstraints = {
    "personal.firstName": { "required": true, "size": "2..50" },
    "personal.lastName": { "required": true, "size": "2..50" },
    "contacts.*.name": { "required": true, "type": "string" },
    "contacts.*.relationship": { "required": true, "inList": "spouse,parent,sibling,friend" },
    "contacts.*.phone": { "required": true, "regex": "^\d{3}-\d{3}-\d{4}$" }
};
```

### Configuration Validation

Validate application configuration files:

```groovy
// App config with database connections and feature flags
var configConstraints = {
    "database.connections.*.host": { "required": true, "type": "string" },
    "database.connections.*.port": { "type": "numeric", "range": "1..65535" },
    "features.*.name": { "required": true, "type": "string" },
    "features.*.enabled": { "required": true, "type": "boolean" },
    "logging.handlers.*.level": { "inList": "debug,info,warn,error" }
};
```

Field name shortcuts make CBValidation incredibly powerful for handling real-world data structures while keeping your constraint definitions clean and maintainable.
