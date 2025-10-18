---
description: Using the Validatable delegate for object validation
icon: shuffle
---

# Validatable Delegate

The `Validatable@cbValidation` delegate (ColdBox 7+) allows you to add validation capabilities directly to any object using ColdBox's delegation pattern. This provides a cleaner, more object-oriented approach to validation.

## Overview

Instead of injecting the `ValidationManager` into every class, you can delegate validation methods directly to your objects. This is available in ColdBox 7 and later.

## Basic Setup

### Shorthand Syntax

The simplest way to make an object validatable:

{% tabs %}
{% tab title="BoxLang" %}
{% code title="User.bx" overflow="wrap" lineNumbers="true" %}
```groovy
class delegates="Validatable@cbValidation" {

    property name="id" type="numeric";
    property name="name" type="string";
    property name="email" type="string";

    this.constraints = {
        name: { required: true, size: "2..100" },
        email: { required: true, type: "email" }
    };
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="User.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
component delegates="Validatable@cbValidation" {
    property name="id" type="numeric";
    property name="name" type="string";
    property name="email" type="string";

    this.constraints = {
        name = { required = true, size = "2..100" },
        email = { required = true, type = "email" }
    };
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Selective Method Delegation

Delegate only specific validation methods:

{% tabs %}
{% tab title="BoxLang" %}
```groovy
class delegates="Validatable@cbValidation=validate,validateOrFail" {
    property name="sku" type="string";
    property name="price" type="numeric";
}
```

{% code title="product.bx" overflow="wrap" lineNumbers="true" %}
```groovy
// Only add validate and validateOrFail methods
component delegates="Validatable@cbValidation=validate,validateOrFail" {
    property name="sku" type="string";
    property name="price" type="numeric";
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="product.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
// Only add validate and validateOrFail methods
component delegates="Validatable@cbValidation=validate,validateOrFail" {
    property name="sku" type="string";
    property name="price" type="numeric";
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Long Syntax with Property Injection

For more control, use explicit property delegation:

{% tabs %}
{% tab title="BoxLang" %}
{% code title="order.bx" overflow="wrap" lineNumbers="true" %}
```groovy
class {
    property name="validatable"
        inject="Validatable@cbValidation"
        delegate="validate,validateOrFail";

    property name="orderNumber" type="string";
    property name="items" type="array";

    this.constraints = {
        orderNumber: { required: true },
        items: { required: true, arrayItem: {...} }
    };
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="order.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
component {
    property name="validatable"
        inject="Validatable@cbValidation"
        delegate="validate,validateOrFail";

    property name="orderNumber" type="string";
    property name="items" type="array";

    this.constraints = {
        orderNumber = { required = true },
        items = { required = true, arrayItem = {...} }
    };
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Available Delegated Methods

When using the Validatable delegate, the following methods are available:

* `validate()` - Validate and return result object
* `validateOrFail()` - Validate or throw exception
* `isValid()` - Quick boolean check (stores results)
* `getValidationResults()` - Get stored validation results
* `validateHasValue()` - Check if a value exists
* `validateIsNullOrEmpty()` - Check if value is null/empty
* `assert()` - Assert a condition is true
* `getValidationManager()` - Access the ValidationManager

## Using Delegated Validation Methods

### Validate Method

{% tabs %}
{% tab title="BoxLang" %}
{% code title="handler.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function saveUser(event, rc, prc) {
    var user = entityNew("User");
    populateModel(user);

    // Call validate() directly on the object
    var results = user.validate();

    if (results.hasErrors()) {
        prc.errors = results.getAllErrors();
        return event.setView("user/form");
    }

    entitySave(user);
    return event.setNextRoute("user.view");
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="handler.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function saveUser(event, rc, prc) {
    var user = entityNew("User");
    populateModel(user);

    // Call validate() directly on the object
    var results = user.validate();

    if (results.hasErrors()) {
        prc.errors = results.getAllErrors();
        return event.setView("user/form");
    }

    entitySave(user);
    return event.setNextRoute("user.view");
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### ValidateOrFail Method

For API endpoints with exception handling:

{% tabs %}
{% tab title="BoxLang" %}
{% code title="api.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function apiCreateUser(event, rc, prc) {
    var user = entityNew("User");
    populateModel(user);

    try {
        // validateOrFail throws exception if validation fails
        user.validateOrFail();

        entitySave(user);
        return event.renderData(data: user);
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
{% code title="api.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function apiCreateUser(event, rc, prc) {
    var user = entityNew("User");
    populateModel(user);

    try {
        // validateOrFail throws exception if validation fails
        user.validateOrFail();

        entitySave(user);
        return event.renderData(data=user);
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

### IsValid Method

Quick validation with stored results:

{% tabs %}
{% tab title="BoxLang" %}
{% code title="validation-check.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function processUser(event, rc, prc) {
    var user = entityNew("User");
    populateModel(user);

    // isValid() stores results and returns boolean
    if (user.isValid()) {
        // User is valid, proceed
        userService.create(user);
    } else {
        // Access stored results
        var results = user.getValidationResults();
        prc.errors = results.getAllErrors();
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="validation-check.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function processUser(event, rc, prc) {
    var user = entityNew("User");
    populateModel(user);

    // isValid() stores results and returns boolean
    if (user.isValid()) {
        // User is valid, proceed
        userService.create(user);
    } else {
        // Access stored results
        var results = user.getValidationResults();
        prc.errors = results.getAllErrors();
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Advanced Usage

### Validation with Profiles

Pass profiles for targeted validation:

{% tabs %}
{% tab title="BoxLang" %}
{% code title="profile-validation.bx" overflow="wrap" lineNumbers="true" %}
```groovy
component delegates="Validatable@cbValidation" {
    property name="name" type="string";
    property name="email" type="string";
    property name="password" type="string";

    this.constraints = {
        name: { required: true },
        email: { required: true, type: "email" },
        password: { required: true, size: "8..50" }
    };

    this.constraintProfiles = {
        registration: "name,email,password",
        update: "name,email",
        passwordChange: "password"
    };
}

// In handler:
function updateUser(event, rc, prc) {
    var user = userService.getById(rc.userId);
    populateModel(user);

    // Validate only update profile fields
    if (user.isValid(profiles: "update")) {
        userService.update(user);
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="profile-validation.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
component delegates="Validatable@cbValidation" {
    property name="name" type="string";
    property name="email" type="string";
    property name="password" type="string";

    this.constraints = {
        name = { required = true },
        email = { required = true, type = "email" },
        password = { required = true, size = "8..50" }
    };

    this.constraintProfiles = {
        registration = "name,email,password",
        update = "name,email",
        passwordChange = "password"
    };
}

// In handler:
function updateUser(event, rc, prc) {
    var user = userService.getById(rc.userId);
    populateModel(user);

    // Validate only update profile fields
    if (user.isValid(profiles="update")) {
        userService.update(user);
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Custom Validation with Callbacks

Combine validation with business logic:

{% tabs %}
{% tab title="BoxLang" %}
{% code title="callback-validation.bx" overflow="wrap" lineNumbers="true" %}
```groovy
component delegates="Validatable@cbValidation" {
    property name="sku" type="string";
    property name="price" type="numeric";

    this.constraints = {
        sku: { required: true, regex: "^[A-Z0-9]+$" },
        price: { required: true, min: 0.01 }
    };

    // Custom validation method
    public void function validateUniqueSku() {
        if (productService.skuExists(this.sku)) {
            throw("ValidationException", "SKU '#sku#' already exists");
        }
    }
}

// In handler:
function createProduct(event, rc, prc) {
    var product = entityNew("Product");
    populateModel(product);

    try {
        product.validateOrFail();
        product.validateUniqueSku();  // Custom validation

        productService.create(product);
        return event.setNextRoute("product.view");
    } catch(ValidationException e) {
        prc.errors = [e.message];
    }
}
```
{% endcode %}
{% endtab %}

{% tab title="CFML" %}
{% code title="callback-validation.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
component delegates="Validatable@cbValidation" {
    property name="sku" type="string";
    property name="price" type="numeric";

    this.constraints = {
        sku = { required = true, regex = "^[A-Z0-9]+$" },
        price = { required = true, min = 0.01 }
    };

    // Custom validation method
    public void function validateUniqueSku() {
        if (productService.skuExists(this.sku)) {
            throw("ValidationException", "SKU '#sku#' already exists");
        }
    }
}

// In handler:
function createProduct(event, rc, prc) {
    var product = entityNew("Product");
    populateModel(product);

    try {
        product.validateOrFail();
        product.validateUniqueSku();  // Custom validation

        productService.create(product);
        return event.setNextRoute("product.view");
    } catch(ValidationException e) {
        prc.errors = [e.message];
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Best Practices

### 1. Use Shorthand for Full Delegation

For simple validation needs, use the shorthand syntax:

```javascript
component delegates="Validatable@cbValidation" {
    // All validation methods available
}
```

### 2. Use Selective Delegation for Lightweight Objects

For objects that only need specific methods:

```javascript
component delegates="Validatable@cbValidation=validate,isValid" {
    // Only these methods available
}
```

### 3. Always Define Constraints

Ensure your object has constraints defined:

```javascript
component delegates="Validatable@cbValidation" {
    this.constraints = {
        // Define all constraints here
    };
}
```

### 4. Use isValid() for Simple Checks

For quick validation without error details:

```javascript
if (user.isValid()) {
    processUser(user);
}
```

### 5. Use validateOrFail() for APIs

For REST endpoints where you need exception handling:

```javascript
try {
    user.validateOrFail();
    // Process user
} catch(ValidationException e) {
    return errorResponse(e);
}
```

### 6. Combine with Custom Methods

Add custom validation logic to your objects:

```javascript
public boolean function isEligible() {
    return this.age >= 18 && this.isValid(profiles="eligibility");
}
```

## Supported ColdBox Versions

The Validatable delegate requires:

* **ColdBox 7.0+**
* **cbValidation 4.1.0+**

For earlier ColdBox versions, use the mixin methods via `validate()` and `validateOrFail()`.

## See Also

* [Installation & Mixins](../installation.md) - Mixin methods for handlers, views, interceptors
* [Validation Results](../overview/) - Understanding validation result objects
* [Custom Validators](advanced-custom-validators.md) - Creating custom validation logic
