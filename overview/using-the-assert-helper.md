---
description: Using the assert() helper method
icon: hand-holding-medical
---

# Using the Assert Helper

The `assert()` helper method allows you to validate conditions and throw exceptions if they fail. It's useful for:

* Checking pre-conditions before processing
* Validating method arguments
* Ensuring business logic invariants

The `assert()` method was introduced in 4.3.1.

## Overview

The `assert()` method mimics Java's `assert` keyword. It evaluates a condition to a boolean and must be true to pass. If false or null, it throws an `AssertError` exception.

## Basic Usage

{% tabs %}
{% tab title="BoxLang" %}
{% code title="assertions.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function processOrder(orderId) {
    // Assert that orderId is provided
    assert(orderId > 0, "Order ID must be greater than 0");

    var order = orderService.getById(orderId);

    // Assert that order exists
    assert(!isNull(order), "Order not found");

    // Assert order is in correct status
    assert(order.status == "pending", "Order must be pending to process");

    // Process the order
    return processPayment(order);
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="assertions.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function processOrder(orderId) {
    // Assert that orderId is provided
    assert(orderId > 0, "Order ID must be greater than 0");

    var order = orderService.getById(orderId);

    // Assert that order exists
    assert(!isNull(order), "Order not found");

    // Assert order is in correct status
    assert(order.status == "pending", "Order must be pending to process");

    // Process the order
    return processPayment(order);
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Syntax

```javascript
boolean function assert(
    required target,
    string message = ""
)
```

**Parameters:**

* `target` - The value to evaluate as boolean (required)
* `message` - Custom error message (optional)

**Returns:** `true` if assertion passes

**Throws:** `AssertError` if assertion fails

## Return Values

{% tabs %}
{% tab title="BoxLang" %}
```groovy
// ✅ Returns true when condition is true
if (assert(true, "This passes")) {
    writeOutput("Assertion passed!");  // Executes
}

// ❌ Throws AssertError when condition is false
try {
    assert(false, "This fails");
} catch(AssertError e) {
    writeOutput(e.message);  // "This fails"
}

// ❌ Throws AssertError when target is null
try {
    var value = javacast("null", "");
    assert(value, "Value cannot be null");
} catch(AssertError e) {
    writeOutput(e.message);  // "Value cannot be null"
}
```
{% endtab %}

{% tab title="CFML" %}
```javascript
// ✅ Returns true when condition is true
if (assert(true, "This passes")) {
    writeOutput("Assertion passed!");  // Executes
}

// ❌ Throws AssertError when condition is false
try {
    assert(false, "This fails");
} catch(AssertError e) {
    writeOutput(e.message);  // "This fails"
}

// ❌ Throws AssertError when target is null
try {
    var value = javacast("null", "");
    assert(value, "Value cannot be null");
} catch(AssertError e) {
    writeOutput(e.message);  // "Value cannot be null"
}
```
{% endtab %}
{% endtabs %}

## Common Patterns

### Null Checking

{% tabs %}
{% tab title="BoxLang" %}
{% code title="null-checking.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function saveUser(user) {
    // Assert user is not null
    assert(!isNull(user), "User object cannot be null");

    // Assert required fields
    assert(len(user.name) > 0, "User name is required");
    assert(len(user.email) > 0, "User email is required");

    return userService.save(user);
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="null-checking.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function saveUser(user) {
    // Assert user is not null
    assert(!isNull(user), "User object cannot be null");

    // Assert required fields
    assert(len(user.name) > 0, "User name is required");
    assert(len(user.email) > 0, "User email is required");

    return userService.save(user);
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Range Validation

{% tabs %}
{% tab title="BoxLang" %}
{% code title="range-validation.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function setDiscount(percentage) {
    // Ensure percentage is within valid range
    assert(percentage >= 0, "Discount cannot be negative");
    assert(percentage <= 100, "Discount cannot exceed 100%");
    assert(percentage == int(percentage), "Discount must be a whole number");

    this.discountPercentage = percentage;
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="range-validation.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function setDiscount(percentage) {
    // Ensure percentage is within valid range
    assert(percentage >= 0, "Discount cannot be negative");
    assert(percentage <= 100, "Discount cannot exceed 100%");
    assert(percentage == int(percentage), "Discount must be a whole number");

    this.discountPercentage = percentage;
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Array/Collection Validation

{% tabs %}
{% tab title="BoxLang" %}
{% code title="collection-validation.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function processItems(items) {
    // Assert collection is not empty
    assert(isArray(items) && items.len() > 0, "Items array cannot be empty");

    // Assert maximum number of items
    assert(items.len() <= 1000, "Cannot process more than 1000 items");

    for (var item in items) {
        assert(!isNull(item.id), "Each item must have an ID");
    }

    return itemService.bulkProcess(items);
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="collection-validation.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function processItems(items) {
    // Assert collection is not empty
    assert(isArray(items) && arrayLen(items) > 0, "Items array cannot be empty");

    // Assert maximum number of items
    assert(arrayLen(items) <= 1000, "Cannot process more than 1000 items");

    for (var item in items) {
        assert(!isNull(item.id), "Each item must have an ID");
    }

    return itemService.bulkProcess(items);
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### State Validation

{% tabs %}
{% tab title="BoxLang" %}
{% code title="state-validation.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function deleteOrder(order) {
    // Assert order is in a state that can be deleted
    assert(
        order.status == "pending" || order.status == "cancelled",
        "Only pending or cancelled orders can be deleted"
    );

    // Assert no payments have been processed
    assert(order.paymentStatus != "completed", "Cannot delete order with completed payment");

    return orderService.delete(order);
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="state-validation.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function deleteOrder(order) {
    // Assert order is in a state that can be deleted
    assert(
        order.status == "pending" || order.status == "cancelled",
        "Only pending or cancelled orders can be deleted"
    );

    // Assert no payments have been processed
    assert(order.paymentStatus != "completed", "Cannot delete order with completed payment");

    return orderService.delete(order);
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### API Input Validation

{% tabs %}
{% tab title="BoxLang" %}
{% code title="api-validation.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function apiUpdateUser(event, rc, prc) {
    try {
        // Assert required fields
        assert(!isNull(rc.userId), "userId is required");
        assert(rc.userId > 0, "userId must be a positive number");
        assert(!isNull(rc.data), "data object is required");
        assert(isStruct(rc.data), "data must be a struct");

        var user = userService.getById(rc.userId);
        assert(!isNull(user), "User not found");

        user = populateModel(user, rc.data);
        user.validateOrFail();

        userService.update(user);
        return event.renderData(data: user);
    } catch(AssertError e) {
        return event.renderData(
            statusCode: 400,
            data: { error: e.message }
        );
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="api-validation.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function apiUpdateUser(event, rc, prc) {
    try {
        // Assert required fields
        assert(!isNull(rc.userId), "userId is required");
        assert(rc.userId > 0, "userId must be a positive number");
        assert(!isNull(rc.data), "data object is required");
        assert(isStruct(rc.data), "data must be a struct");

        var user = userService.getById(rc.userId);
        assert(!isNull(user), "User not found");

        user = populateModel(user, rc.data);
        user.validateOrFail();

        userService.update(user);
        return event.renderData(data=user);
    } catch(AssertError e) {
        return event.renderData(
            statusCode=400,
            data={ error: e.message }
        );
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Assert vs Validate

Choose the right tool for the job:

### Use `assert()` when:

* Checking **pre-conditions** before processing
* Validating **method arguments**
* Ensuring **business logic invariants**
* You want quick **fail-fast** behavior
* Condition is simple and **boolean**

```javascript
assert(quantity > 0, "Quantity must be greater than 0");
```

### Use `validate()` when:

* Validating **complex objects** with multiple rules
* Collecting **multiple errors** from one check
* Validating **form data** or **user input**
* You need **detailed error messages** and fields
* Working with **constraint definitions**

```javascript
var results = validate(target: product, constraints: productConstraints);
```

## Error Handling

### Catching AssertError

{% tabs %}
{% tab title="BoxLang" %}
{% code title="error-handling.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function processData(data) {
    try {
        assert(!isNull(data), "Data cannot be null");
        assert(isStruct(data), "Data must be a struct");
        assert(len(data) > 0, "Data cannot be empty");

        return service.process(data);
    } catch(AssertError e) {
        // Handle assertion failures
        logError("Assertion failed: #e.message#");
        return { success: false, error: e.message };
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="error-handling.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function processData(data) {
    try {
        assert(!isNull(data), "Data cannot be null");
        assert(isStruct(data), "Data must be a struct");
        assert(len(data) > 0, "Data cannot be empty");

        return service.process(data);
    } catch(AssertError e) {
        // Handle assertion failures
        logError("Assertion failed: #e.message#");
        return { success: false, error: e.message };
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Best Practices

### 1. Use Clear, Specific Messages

```javascript
// ✅ Good
assert(price > 0, "Product price must be greater than 0");

// ❌ Vague
assert(price > 0, "Invalid price");
```

### 2. Fail Fast

Check conditions as early as possible in your function:

```javascript
function processOrder(order) {
    // Check all pre-conditions first
    assert(!isNull(order), "Order cannot be null");
    assert(order.status == "pending", "Order must be pending");
    assert(order.total > 0, "Order total must be greater than 0");

    // Then do the actual processing
    return completeOrder(order);
}
```

### 3. Don't Overuse Assert

Assertions are for **programmer errors**, not **user input**:

```javascript
// ✅ Good - programmer error
assert(arrayLen(items) > 0, "Items array should never be empty here");

// ❌ Bad - user input should use validate()
assert(rc.age >= 18, "User must be 18 or older");  // Use validate() instead
```

### 4. Use for Invariants

Assert the state that should always be true:

```javascript
function calculateTotal(items) {
    var total = 0;
    for (var item in items) {
        total += item.price * item.quantity;
    }

    // This total should never be negative
    assert(total >= 0, "Total cannot be negative");

    return total;
}
```

## See Also

* [Validation](./) - Full validation system
* [Installation & Mixins](../installation.md) - Available helper methods
* [Validatable Delegate](../advanced/validatable-delegate.md) - Object validation delegation
