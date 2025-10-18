---
description: Using default values in constraints
---

# Default Values in Constraints

The `defaultValue` constraint (introduced in 4.5.0) allows you to automatically apply default values to fields before validation rules are checked. This is useful for initializing optional fields with sensible defaults.

## Overview

When a field has a `defaultValue` defined and the field's current value is null or empty, the default value will be assigned to the field before any other constraints are evaluated.

## Basic Usage

{% tabs %}

{% tab title="BoxLang" %}

```javascript
class {
    this.constraints = {
        status: {
            defaultValue: "active",
            required: true,
            inList: "active,inactive,pending"
        },
        createdDate: {
            defaultValue: now(),
            required: true,
            type: "date"
        },
        priority: {
            defaultValue: 5,
            min: 1,
            max: 10
        }
    };
}
```

{% endtab %}

{% tab title="CFML" %}

```javascript
component {
    this.constraints = {
        status: {
            defaultValue = "active",
            required = true,
            inList = "active,inactive,pending"
        },
        createdDate = {
            defaultValue = now(),
            required = true,
            type = "date"
        },
        priority = {
            defaultValue = 5,
            min = 1,
            max = 10
        }
    };
}
```

{% endtab %}

{% endtabs %}

## When Defaults Are Applied

The `defaultValue` is applied when:

- The field value is **null**
- The field value is an **empty string** (for simple values)
- The field value has **no value** (hasValue() check fails)

The default is **NOT** applied when:

- The field already has a non-empty value
- The field is explicitly set to a falsy value like `false` or `0`

## Practical Examples

### Form with Default Status

{% tabs %}

{% tab title="BoxLang" %}

{% code title="User.bx" overflow="wrap" lineNumbers="true" %}
```javascript
class {
    property name="name" type="string";
    property name="email" type="string";
    property name="status" type="string";
    property name="roleId" type="numeric";

    this.constraints = {
        name: { required: true, size: "2..100" },
        email: { required: true, type: "email" },
        status: {
            defaultValue: "pending",
            required: true,
            inList: "pending,active,inactive"
        },
        roleId: {
            defaultValue: 3,  // Default user role
            required: true,
            type: "numeric"
        }
    };
}
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="User.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
component {
    property name="name" type="string";
    property name="email" type="string";
    property name="status" type="string";
    property name="roleId" type="numeric";

    this.constraints = {
        name = { required = true, size = "2..100" },
        email = { required = true, type = "email" },
        status = {
            defaultValue = "pending",
            required = true,
            inList = "pending,active,inactive"
        },
        roleId = {
            defaultValue = 3,  // Default user role
            required = true,
            type = "numeric"
        }
    };
}
```
{% endcode %}

{% endtab %}

{% endtabs %}

In your handler:

```javascript
function create(event, rc, prc) {
    var user = new User();
    populateModel(user);

    var results = validate(target=user);

    if (!results.hasErrors()) {
        // user.status will be "pending" if not provided
        // user.roleId will be 3 if not provided
        userService.create(user);
    }
}
```

### Dynamic Default with Functions

You can use closures to compute dynamic defaults:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="Order.bx" overflow="wrap" lineNumbers="true" %}
```javascript
class {
    property name="orderNumber" type="string";
    property name="createdDate" type="date";
    property name="expiryDate" type="date";

    this.constraints = {
        orderNumber: {
            defaultValue: () => createUUID(),
            required: true,
            type: "string"
        },
        createdDate: {
            defaultValue: () => now(),
            required: true,
            type: "date"
        },
        expiryDate: {
            defaultValue: () => dateAdd('d', 30, now()),
            required: true,
            type: "date"
        }
    };
}
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="Order.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
component {
    property name="orderNumber" type="string";
    property name="createdDate" type="date";
    property name="expiryDate" type="date";

    this.constraints = {
        orderNumber = {
            defaultValue = function() { return createUUID(); },
            required = true,
            type = "string"
        },
        createdDate = {
            defaultValue = function() { return now(); },
            required = true,
            type = "date"
        },
        expiryDate = {
            defaultValue = function() { return dateAdd('d', 30, now()); },
            required = true,
            type = "date"
        }
    };
}
```
{% endcode %}

{% endtab %}

{% endtabs %}

## API Validation with Defaults

{% tabs %}

{% tab title="BoxLang" %}

{% code title="ProductHandler.bx" overflow="wrap" lineNumbers="true" %}
```javascript
function apiCreateProduct(event, rc, prc) {
    var productConstraints = {
        name: { required: true, size: "1..255" },
        description: { size: "0..1000" },
        active: {
            defaultValue: true,
            type: "boolean"
        },
        publishedDate: {
            defaultValue: () => now(),
            required: false
        }
    };

    try {
        // Apply defaults and validate
        var validProduct = validateOrFail(
            target: rc,
            constraints: productConstraints
        );

        var product = productService.create(validProduct);
        return event.renderData(data=product);
    } catch(ValidationException e) {
        return event.renderData(
            statusCode: 422,
            data: { errors: deserializeJSON(e.extendedInfo) }
        );
    }
}
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="ProductHandler.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function apiCreateProduct(event, rc, prc) {
    var productConstraints = {
        name = { required = true, size = "1..255" },
        description = { size = "0..1000" },
        active = {
            defaultValue = true,
            type = "boolean"
        },
        publishedDate = {
            defaultValue = function() { return now(); },
            required = false
        }
    };

    try {
        // Apply defaults and validate
        var validProduct = validateOrFail(
            target=rc,
            constraints=productConstraints
        );

        var product = productService.create(validProduct);
        return event.renderData(data=product);
    } catch(ValidationException e) {
        return event.renderData(
            statusCode=422,
            data={ errors: deserializeJSON(e.extendedInfo) }
        );
    }
}
```
{% endcode %}

{% endtab %}

{% endtabs %}

## Best Practices

### 1. Use for Optional Fields Only

Default values work best for truly optional fields:
```javascript
// Good - truly optional
optionalNotes: { defaultValue: "", size: "0..500" }

// Less ideal - required field should not have a default that bypasses validation
email: { defaultValue: "noemail@example.com", required: true, type: "email" }
```

### 2. Defaults Should Match Constraint Rules

Always ensure your default value complies with other constraints:

{% tabs %}

{% tab title="BoxLang" %}

```javascript
// ✅ Good - default matches type and range constraints
priority: {
    defaultValue: 5,
    type: "numeric",
    min: 1,
    max: 10
}

// ❌ Bad - default violates inList constraint
status: {
    defaultValue: "unknown",
    inList: "active,inactive,pending"
}
```

{% endtab %}

{% tab title="CFML" %}

```javascript
// ✅ Good - default matches type and range constraints
priority = {
    defaultValue = 5,
    type = "numeric",
    min = 1,
    max = 10
}

// ❌ Bad - default violates inList constraint
status = {
    defaultValue = "unknown",
    inList = "active,inactive,pending"
}
```

{% endtab %}

{% endtabs %}

### 3. Use Functions for Dynamic Defaults

For timestamp or unique identifier defaults, use functions:

```javascript
createdAt: {
    defaultValue: () => now(),
    required: true
}

uuid: {
    defaultValue: () => createUUID(),
    required: true
}
```

### 4. Document Default Behavior

Always document which fields have defaults:

```javascript
/**
 * User entity constraints
 *
 * Fields with defaults:
 * - status: defaults to "pending"
 * - roleId: defaults to 3 (user role)
 * - createdDate: defaults to now()
 */
```

## See Also

- [Constraint Profiles](validating-with-profiles.md) - Validate different field sets for different scenarios
- [Custom Constraints](../../advanced/constraint-custom-messages.md) - Customize validation messages
- [Validating Constraints](README.md) - Main validation documentation
