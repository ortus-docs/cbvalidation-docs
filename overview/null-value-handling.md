---
description: Understanding null value handling in validation
---

# Null Value Handling

CBValidation handles null values carefully to ensure robust validation across nested structures and arrays. This guide explains how null values are treated during validation and filtering.

## Overview

Null value handling (enhanced in 4.7.0-4.8.0) ensures that:
- Null values don't cause validation errors unexpectedly
- Nested structures and arrays properly filter null values
- The `validateOrFail()` method returns clean, null-free results

## Validation Rules for Nulls

### Required Fields

Null values are treated as missing when `required: true`:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="validation.bx" overflow="wrap" lineNumbers="true" %}
```groovy
var constraints = {
    email: { required: true, type: "email" }
};

// ❌ FAILS - null is treated as missing
var result = validate(target={ email: javacast("null", "") }, constraints: constraints);
result.hasErrors();  // true - "email is required"

// ❌ FAILS - empty string is treated as missing
var result = validate(target={ email: "" }, constraints: constraints);
result.hasErrors();  // true - "email is required"

// ✅ PASSES - value provided
var result = validate(target={ email: "test@example.com" }, constraints: constraints);
result.hasErrors();  // false
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="validation.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
var constraints = {
    email = { required = true, type = "email" }
};

// ❌ FAILS - null is treated as missing
var result = validate(target={ email: javacast("null", "") }, constraints=constraints);
result.hasErrors();  // true - "email is required"

// ❌ FAILS - empty string is treated as missing
var result = validate(target={ email: "" }, constraints=constraints);
result.hasErrors();  // true - "email is required"

// ✅ PASSES - value provided
var result = validate(target={ email: "test@example.com" }, constraints=constraints);
result.hasErrors();  // false
```
{% endcode %}

{% endtab %}

{% endtabs %}

### Optional Fields with Type Checking

When `required: false` (default), null values are generally ignored by type validators:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="optional-validation.bx" overflow="wrap" lineNumbers="true" %}
```groovy
var constraints = {
    phone: { type: "string", size: "10..20" }  // not required
};

// ✅ PASSES - null is ignored for optional fields
var result = validate(target={ phone: javacast("null", "") }, constraints: constraints);
result.hasErrors();  // false

// ✅ PASSES - empty string ignored (treated as no value)
var result = validate(target={ phone: "" }, constraints: constraints);
result.hasErrors();  // false

// ✅ PASSES - valid value
var result = validate(target={ phone: "555-1234-5678" }, constraints: constraints);
result.hasErrors();  // false

// ❌ FAILS - provided value doesn't meet size constraint
var result = validate(target={ phone: "555" }, constraints: constraints);
result.hasErrors();  // true
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="optional-validation.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
var constraints = {
    phone = { type = "string", size = "10..20" }  // not required
};

// ✅ PASSES - null is ignored for optional fields
var result = validate(target={ phone: javacast("null", "") }, constraints=constraints);
result.hasErrors();  // false

// ✅ PASSES - empty string ignored (treated as no value)
var result = validate(target={ phone: "" }, constraints=constraints);
result.hasErrors();  // false

// ✅ PASSES - valid value
var result = validate(target={ phone: "555-1234-5678" }, constraints=constraints);
result.hasErrors();  // false

// ❌ FAILS - provided value doesn't meet size constraint
var result = validate(target={ phone: "555" }, constraints=constraints);
result.hasErrors();  // true
```
{% endcode %}

{% endtab %}

{% endtabs %}

## Null Filtering in validateOrFail()

The `validateOrFail()` method filters out null values from results (4.7.0+). This ensures clean data:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="filterin

g-nulls.bx" overflow="wrap" lineNumbers="true" %}
```groovy
var data = {
    name: "John",
    email: "john@example.com",
    phone: javacast("null", ""),
    address: javacast("null", "")
};

var constraints = {
    name: { required: true },
    email: { required: true, type: "email" },
    phone: { type: "string" },
    address: { type: "string" }
};

// Result contains only non-null fields that matched constraints
var validated = validateOrFail(target: data, constraints: constraints);

// validated = {
//     name: "John",
//     email: "john@example.com"
//     // phone and address removed (were null)
// }
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="filtering-nulls.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
var data = {
    name = "John",
    email = "john@example.com",
    phone = javacast("null", ""),
    address = javacast("null", "")
};

var constraints = {
    name = { required = true },
    email = { required = true, type = "email" },
    phone = { type = "string" },
    address = { type = "string" }
};

// Result contains only non-null fields that matched constraints
var validated = validateOrFail(target=data, constraints=constraints);

// validated = {
//     name = "John",
//     email = "john@example.com"
//     // phone and address removed (were null)
// }
```
{% endcode %}

{% endtab %}

{% endtabs %}

## Nested Structures with Nulls

When validating nested structures, null values in nested objects are properly handled:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="nested-nulls.bx" overflow="wrap" lineNumbers="true" %}
```groovy
var data = {
    name: "John",
    address: {
        street: "123 Main St",
        city: javacast("null", ""),
        state: "CA",
        zip: javacast("null", "")
    }
};

var constraints = {
    name: { required: true },
    address: {
        type: "struct",
        constraints: {
            street: { required: true },
            city: { type: "string" },
            state: { required: true, size: 2 },
            zip: { type: "string" }
        }
    }
};

// validateOrFail returns filtered result without nulls
var validated = validateOrFail(target: data, constraints: constraints);

// validated = {
//     name = "John",
//     address = {
//         street = "123 Main St",
//         state = "CA"
//         // city and zip removed (were null)
//     }
// }
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="nested-nulls.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
var data = {
    name = "John",
    address = {
        street = "123 Main St",
        city = javacast("null", ""),
        state = "CA",
        zip = javacast("null", "")
    }
};

var constraints = {
    name = { required = true },
    address = {
        type = "struct",
        constraints = {
            street = { required = true },
            city = { type = "string" },
            state = { required = true, size = 2 },
            zip = { type = "string" }
        }
    }
};

// validateOrFail returns filtered result without nulls
var validated = validateOrFail(target=data, constraints=constraints);

// validated = {
//     name = "John",
//     address = {
//         street = "123 Main St",
//         state = "CA"
//         // city and zip removed (were null)
//     }
// }
```
{% endcode %}

{% endtab %}

{% endtabs %}

## Array Items with Nulls

Arrays are properly filtered when items contain null values:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="array-nulls.bx" overflow="wrap" lineNumbers="true" %}
```groovy
var data = {
    name: "Product Collection",
    items: [
        { sku: "SKU-001", price: 19.99 },
        { sku: javacast("null", ""), price: javacast("null", "") },
        { sku: "SKU-003", price: 29.99 }
    ]
};

var constraints = {
    name: { required: true },
    items: {
        arrayItem: {
            constraints: {
                sku: { required: true },
                price: { required: true, type: "numeric" }
            }
        }
    }
};

// ❌ FAILS - second item has null values in required fields
try {
    var validated = validateOrFail(target: data, constraints: constraints);
} catch(ValidationException e) {
    // Error: items[2].sku is required
    writeOutput(e.message);
}

// Fix the data:
data.items[2] = { sku: "SKU-002", price: 24.99 };

// ✅ PASSES - all items have values
var validated = validateOrFail(target: data, constraints: constraints);

// All items returned (none were null as a whole)
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="array-nulls.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
var data = {
    name = "Product Collection",
    items = [
        { sku = "SKU-001", price = 19.99 },
        { sku = javacast("null", ""), price = javacast("null", "") },
        { sku = "SKU-003", price = 29.99 }
    ]
};

var constraints = {
    name = { required = true },
    items = {
        arrayItem = {
            constraints = {
                sku = { required = true },
                price = { required = true, type = "numeric" }
            }
        }
    }
};

// ❌ FAILS - second item has null values in required fields
try {
    var validated = validateOrFail(target=data, constraints=constraints);
} catch(ValidationException e) {
    // Error: items[2].sku is required
    writeOutput(e.message);
}

// Fix the data:
data.items[2] = { sku = "SKU-002", price = 24.99 };

// ✅ PASSES - all items have values
var validated = validateOrFail(target=data, constraints=constraints);

// All items returned (none were null as a whole)
```
{% endcode %}

{% endtab %}

{% endtabs %}

## Best Practices

### 1. Null Check Before Processing

Even after validation, check for nulls if you expect them:

```javascript
var result = validate(target: data, constraints: constraints);
if (result.hasErrors()) {
    // Handle errors
} else {
    // data may have nulls in optional fields
    if (isNull(data.phone)) {
        data.phone = "";  // Set a default
    }
}
```

### 2. Use Required for Important Fields

Always mark fields as `required: true` if they're critical:

```javascript
constraints = {
    email: { required: true, type: "email" },       // Required
    phone: { type: "string", size: "10..20" },      // Optional
    address: { type: "string" }                      // Optional
};
```

### 3. Use defaultValue for Optional Fields

Apply sensible defaults for optional fields:

```javascript
constraints = {
    status: {
        defaultValue: "pending",
        required: true,
        inList: "pending,active,inactive"
    }
};
```

### 4. Clean Results with validateOrFail

Use `validateOrFail()` to get clean data without nulls:

```javascript
// Good for API responses - clean data
var cleanData = validateOrFail(target: rc, constraints: constraints);

// Good for forms - keep structure for display
var formResult = validate(target: rc, constraints: constraints);
```

## Empty vs Null

Understand the distinction:

- **Null**: `javacast("null", "")` - No value exists
- **Empty String**: `""` - Value exists but is blank
- **Zero**: `0` or `0.0` - Valid numeric value
- **False**: `false` - Valid boolean value

Most validators treat empty strings the same as null, except in special cases:

```javascript
// Both null and empty string fail required
constraints = { field: { required: true } };

// Both null and empty string pass optional type check
constraints = { field: { type: "string" } };

// Zero and false are NOT treated as empty
constraints = { field: { required: true } };
validate(target: { field: 0 }, constraints: constraints);      // ✅ PASSES
validate(target: { field: false }, constraints: constraints);  // ✅ PASSES
```

## See Also

- [Default Values](using-default-values.md) - Apply defaults before validation
- [Validating Constraints](README.md) - Main validation documentation
- [Custom Validators](../../advanced/advanced-custom-validators.md) - Handle special null logic
