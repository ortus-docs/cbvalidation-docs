---
description: Advanced constraint profiles for targeted validation scenarios
---

# Validating With Profiles

Constraint profiles (introduced in CBValidation 2.0) allow you to validate specific subsets of fields for different scenarios. This enables flexible validation workflows where different operations require different validation rules.

## Overview

Instead of validating all constraints every time, profiles let you:
- Validate only relevant fields for specific operations
- Improve performance by skipping unnecessary validations
- Create context-specific validation rules (registration vs. update vs. password change)
- Support multi-step forms and wizards

## Basic Profile Definition

Define profiles using the `this.constraintProfiles` struct in your object:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="User.bx" overflow="wrap" lineNumbers="true" %}
```groovy
class {
    property name="firstName" type="string";
    property name="lastName" type="string";
    property name="email" type="string";
    property name="password" type="string";
    property name="confirmPassword" type="string";

    this.constraints = {
        firstName: { required: true, size: "2..50" },
        lastName: { required: true, size: "2..50" },
        email: { required: true, type: "email" },
        password: { required: true, size: "8..50" },
        confirmPassword: { required: true, sameAs: "password" }
    };

    this.constraintProfiles = {
        registration: "firstName,lastName,email,password,confirmPassword",
        update: "firstName,lastName,email",
        passwordChange: "password,confirmPassword"
    };
}
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="User.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
component {
    property name="firstName" type="string";
    property name="lastName" type="string";
    property name="email" type="string";
    property name="password" type="string";
    property name="confirmPassword" type="string";

    this.constraints = {
        firstName = { required = true, size = "2..50" },
        lastName = { required = true, size = "2..50" },
        email = { required = true, type = "email" },
        password = { required = true, size = "8..50" },
        confirmPassword = { required = true, sameAs = "password" }
    };

    this.constraintProfiles = {
        registration = "firstName,lastName,email,password,confirmPassword",
        update = "firstName,lastName,email",
        passwordChange = "password,confirmPassword"
    };
}
```
{% endcode %}

{% endtab %}

{% endtabs %}

## Using Profiles

Every validation method accepts a `profiles` argument:

{% tabs %}

{% tab title="BoxLang" %}

```groovy
// Single profile
var result = validate(target: user, profiles: "update");

// Multiple profiles
var result = validate(target: user, profiles: "update,passwordChange");

// With validateOrFail for APIs
try {
    var validUser = validateOrFail(target: user, profiles: "registration");
    userService.create(validUser);
} catch(ValidationException e) {
    // Handle validation errors
}
```

{% endtab %}

{% tab title="CFML" %}

```javascript
// Single profile
var result = validate(target=user, profiles="update");

// Multiple profiles
var result = validate(target=user, profiles="update,passwordChange");

// With validateOrFail for APIs
try {
    var validUser = validateOrFail(target=user, profiles="registration");
    userService.create(validUser);
} catch(ValidationException e) {
    // Handle validation errors
}
```

{% endtab %}

{% endtabs %}

## Complex Profile Scenarios

### Multi-Step Registration Wizard

For complex forms split across multiple steps:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="UserRegistration.bx" overflow="wrap" lineNumbers="true" %}
```groovy
class {
    // Step 1: Basic Info
    property name="firstName" type="string";
    property name="lastName" type="string";
    property name="email" type="string";

    // Step 2: Account Details
    property name="username" type="string";
    property name="password" type="string";
    property name="confirmPassword" type="string";

    // Step 3: Profile Info
    property name="dateOfBirth" type="date";
    property name="phone" type="string";
    property name="address" type="string";

    // Step 4: Preferences
    property name="newsletter" type="boolean";
    property name="marketingEmails" type="boolean";

    this.constraints = {
        firstName: { required: true, size: "2..50", alpha: true },
        lastName: { required: true, size: "2..50", alpha: true },
        email: { required: true, type: "email" },
        username: { required: true, size: "3..20", regex: "^[a-zA-Z0-9_]+$" },
        password: { required: true, size: "8..50", regex: "^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)" },
        confirmPassword: { required: true, sameAs: "password" },
        dateOfBirth: { required: true, type: "date" },
        phone: { type: "telephone" },
        address: { size: "0..200" }
    };

    this.constraintProfiles = {
        step1: "firstName,lastName,email",
        step2: "username,password,confirmPassword",
        step3: "dateOfBirth,phone,address",
        step4: "newsletter,marketingEmails",
        complete: "firstName,lastName,email,username,password,confirmPassword,dateOfBirth"
    };
}
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="UserRegistration.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
component {
    // Step 1: Basic Info
    property name="firstName" type="string";
    property name="lastName" type="string";
    property name="email" type="string";

    // Step 2: Account Details
    property name="username" type="string";
    property name="password" type="string";
    property name="confirmPassword" type="string";

    // Step 3: Profile Info
    property name="dateOfBirth" type="date";
    property name="phone" type="string";
    property name="address" type="string";

    // Step 4: Preferences
    property name="newsletter" type="boolean";
    property name="marketingEmails" type="boolean";

    this.constraints = {
        firstName = { required = true, size = "2..50", alpha = true },
        lastName = { required = true, size = "2..50", alpha = true },
        email = { required = true, type = "email" },
        username = { required = true, size = "3..20", regex = "^[a-zA-Z0-9_]+$" },
        password = { required = true, size = "8..50", regex = "^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)" },
        confirmPassword = { required = true, sameAs = "password" },
        dateOfBirth = { required = true, type = "date" },
        phone = { type = "telephone" },
        address = { size = "0..200" }
    };

    this.constraintProfiles = {
        step1 = "firstName,lastName,email",
        step2 = "username,password,confirmPassword",
        step3 = "dateOfBirth,phone,address",
        step4 = "newsletter,marketingEmails",
        complete = "firstName,lastName,email,username,password,confirmPassword,dateOfBirth"
    };
}
```
{% endcode %}

{% endtab %}

{% endtabs %}

### API Endpoint Profiles

Different API endpoints often need different validation rules:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="Product.bx" overflow="wrap" lineNumbers="true" %}
```groovy
class {
    property name="id" type="numeric";
    property name="name" type="string";
    property name="description" type="string";
    property name="price" type="numeric";
    property name="category" type="string";
    property name="sku" type="string";
    property name="active" type="boolean";
    property name="tags" type="array";

    this.constraints = {
        name: { required: true, size: "1..100" },
        description: { size: "0..1000" },
        price: { required: true, type: "numeric", min: 0 },
        category: { required: true, inList: "electronics,books,clothing,home" },
        sku: { required: true, regex: "^[A-Z0-9-]+$" },
        active: { type: "boolean", defaultValue: true },
        tags: { arrayItem: { type: "string", size: "1..20" } }
    };

    this.constraintProfiles = {
        // Full product creation
        create: "name,description,price,category,sku,active,tags",

        // Quick product creation (minimal required fields)
        quickCreate: "name,price,category,sku",

        // Update existing product (ID required, others optional)
        update: "name,description,price,category,active,tags",

        // Bulk import validation (stricter rules)
        import: "name,price,category,sku",

        // Admin-only fields
        admin: "name,description,price,category,sku,active,tags",

        // Public API (limited fields)
        public: "name,description,price,category"
    };
}
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="Product.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
component {
    property name="id" type="numeric";
    property name="name" type="string";
    property name="description" type="string";
    property name="price" type="numeric";
    property name="category" type="string";
    property name="sku" type="string";
    property name="active" type="boolean";
    property name="tags" type="array";

    this.constraints = {
        name = { required = true, size = "1..100" },
        description = { size = "0..1000" },
        price = { required = true, type = "numeric", min = 0 },
        category = { required = true, inList = "electronics,books,clothing,home" },
        sku = { required = true, regex = "^[A-Z0-9-]+$" },
        active = { type = "boolean", defaultValue = true },
        tags = { arrayItem = { type = "string", size = "1..20" } }
    };

    this.constraintProfiles = {
        // Full product creation
        create = "name,description,price,category,sku,active,tags",

        // Quick product creation (minimal required fields)
        quickCreate = "name,price,category,sku",

        // Update existing product (ID required, others optional)
        update = "name,description,price,category,active,tags",

        // Bulk import validation (stricter rules)
        import = "name,price,category,sku",

        // Admin-only fields
        admin = "name,description,price,category,sku,active,tags",

        // Public API (limited fields)
        public = "name,description,price,category"
    };
}
```
{% endcode %}

{% endtab %}

{% endtabs %}

## API Integration Patterns

### RESTful API Validation

Use profiles to match your API endpoints:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="ProductHandler.bx" overflow="wrap" lineNumbers="true" %}
```groovy
function create(event, rc, prc) {
    try {
        // Use 'create' profile for POST /products
        var validProduct = validateOrFail(
            target: rc,
            constraints: "Product",
            profiles: "create"
        );

        var product = productService.create(validProduct);
        return event.renderData(data: product, statusCode: 201);
    } catch(ValidationException e) {
        return event.renderData(
            statusCode: 422,
            data: { errors: deserializeJSON(e.extendedInfo) }
        );
    }
}

function update(event, rc, prc) {
    try {
        // Use 'update' profile for PUT /products/:id
        var validProduct = validateOrFail(
            target: rc,
            constraints: "Product",
            profiles: "update"
        );

        var product = productService.update(rc.id, validProduct);
        return event.renderData(data: product);
    } catch(ValidationException e) {
        return event.renderData(
            statusCode: 422,
            data: { errors: deserializeJSON(e.extendedInfo) }
        );
    }
}

function bulkImport(event, rc, prc) {
    var results = [];

    for (var item in rc.products) {
        try {
            // Stricter validation for bulk imports
            var validProduct = validateOrFail(
                target: item,
                constraints: "Product",
                profiles: "import"
            );

            results.append({
                status: "success",
                product: productService.create(validProduct)
            });
        } catch(ValidationException e) {
            results.append({
                status: "error",
                errors: deserializeJSON(e.extendedInfo),
                data: item
            });
        }
    }

    return event.renderData(data: results);
}
```
{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="ProductHandler.cfc" overflow="wrap" lineNumbers="true" %}
```javascript
function create(event, rc, prc) {
    try {
        // Use 'create' profile for POST /products
        var validProduct = validateOrFail(
            target=rc,
            constraints="Product",
            profiles="create"
        );

        var product = productService.create(validProduct);
        return event.renderData(data=product, statusCode=201);
    } catch(ValidationException e) {
        return event.renderData(
            statusCode=422,
            data={ errors: deserializeJSON(e.extendedInfo) }
        );
    }
}

function update(event, rc, prc) {
    try {
        // Use 'update' profile for PUT /products/:id
        var validProduct = validateOrFail(
            target=rc,
            constraints="Product",
            profiles="update"
        );

        var product = productService.update(rc.id, validProduct);
        return event.renderData(data=product);
    } catch(ValidationException e) {
        return event.renderData(
            statusCode=422,
            data={ errors: deserializeJSON(e.extendedInfo) }
        );
    }
}

function bulkImport(event, rc, prc) {
    var results = [];

    for (var item in rc.products) {
        try {
            // Stricter validation for bulk imports
            var validProduct = validateOrFail(
                target=item,
                constraints="Product",
                profiles="import"
            );

            results.append({
                status = "success",
                product = productService.create(validProduct)
            });
        } catch(ValidationException e) {
            results.append({
                status = "error",
                errors = deserializeJSON(e.extendedInfo),
                data = item
            });
        }
    }

    return event.renderData(data=results);
}
```
{% endcode %}

{% endtab %}

{% endtabs %}

### Role-Based Validation

Different user roles may have different validation requirements:

{% tabs %}

{% tab title="BoxLang" %}

```groovy
function validateByRole(user, data, role) {
    var profile = switch(role) {
        case "admin": "admin";
        case "manager": "update,category";
        case "user": "public";
        default: "public";
    };

    return validate(
        target: data,
        constraints: "Product",
        profiles: profile
    );
}
```

{% endtab %}

{% tab title="CFML" %}

```javascript
function validateByRole(user, data, role) {
    var profile = "";
    switch(role) {
        case "admin":
            profile = "admin";
            break;
        case "manager":
            profile = "update,category";
            break;
        default:
            profile = "public";
    }

    return validate(
        target=data,
        constraints="Product",
        profiles=profile
    );
}
```

{% endtab %}

{% endtabs %}

## Performance Considerations

### Profile Selection Strategy

Choose profiles wisely to optimize performance:

```javascript
// ✅ GOOD - Validates only 3 fields
var result = validate(target=user, profiles="passwordChange");

// ❌ LESS EFFICIENT - Validates all 15+ fields
var result = validate(target=user);

// ✅ GOOD - Multiple targeted profiles
var result = validate(target=user, profiles="update,preferences");

// ❌ AVOID - Overlapping profiles create redundant validation
var result = validate(target=user, profiles="update,admin,complete");
```

### Caching Constraint Definitions

For high-performance scenarios, cache constraint lookups:

{% tabs %}

{% tab title="BoxLang" %}

```groovy
// In your service layer
class ProductService {
    property name="validationManager" inject="ValidationManager@cbvalidation";
    property name="constraintCache" type="struct";

    function init() {
        this.constraintCache = {};
        return this;
    }

    function validateProduct(product, profile = "create") {
        var cacheKey = "Product_" & profile;

        if (!this.constraintCache.keyExists(cacheKey)) {
            // Cache the constraint definition for reuse
            this.constraintCache[cacheKey] = validationManager.getConstraints("Product");
        }

        return validationManager.validate(
            target: product,
            constraints: this.constraintCache[cacheKey],
            profiles: profile
        );
    }
}
```

{% endtab %}

{% tab title="CFML" %}

```javascript
// In your service layer
component {
    property name="validationManager" inject="ValidationManager@cbvalidation";
    property name="constraintCache" type="struct";

    function init() {
        variables.constraintCache = {};
        return this;
    }

    function validateProduct(product, profile = "create") {
        var cacheKey = "Product_" & profile;

        if (!variables.constraintCache.keyExists(cacheKey)) {
            // Cache the constraint definition for reuse
            variables.constraintCache[cacheKey] = validationManager.getConstraints("Product");
        }

        return validationManager.validate(
            target=product,
            constraints=variables.constraintCache[cacheKey],
            profiles=profile
        );
    }
}
```

{% endtab %}

{% endtabs %}

### Profile Size Optimization

Keep profiles focused and avoid overlap:

```javascript
// ✅ FOCUSED PROFILES - Clear purpose, minimal overlap
this.constraintProfiles = {
    create: "name,email,password",           // New user signup
    login: "email,password",                 // Authentication
    profile: "name,bio,avatar",              // Profile updates
    security: "password,confirmPassword,currentPassword"  // Security changes
};

// ❌ OVERLAPPING PROFILES - Redundant validations
this.constraintProfiles = {
    basic: "name,email",
    full: "name,email,password,bio,avatar",  // Contains 'basic' fields
    admin: "name,email,password,bio,avatar,role,permissions"  // Contains 'full' fields
};
```

## Best Practices

### 1. Use Descriptive Profile Names

```javascript
// ✅ CLEAR - Purpose is obvious
registration: "firstName,lastName,email,password,confirmPassword"
passwordReset: "password,confirmPassword,token"
profileUpdate: "firstName,lastName,bio,avatar"

// ❌ UNCLEAR - Hard to understand purpose
p1: "firstName,lastName,email"
basic: "password,confirmPassword"
```

### 2. Group Related Operations

```javascript
// ✅ LOGICAL GROUPING
this.constraintProfiles = {
    // Authentication flows
    registration: "email,password,confirmPassword",
    login: "email,password",
    passwordReset: "password,confirmPassword,resetToken",

    // Profile management
    profileBasic: "firstName,lastName,bio",
    profileComplete: "firstName,lastName,bio,avatar,phone,address",

    // Administrative
    adminCreate: "email,firstName,lastName,role,permissions",
    adminUpdate: "firstName,lastName,role,permissions"
};
```

### 3. Document Profile Usage

```javascript
/**
 * User Constraint Profiles
 *
 * registration - New user signup (email + password)
 * login - User authentication (email + password only)
 * profileUpdate - Update user information (no sensitive fields)
 * passwordChange - Change password (requires current + new password)
 * adminEdit - Administrative user editing (includes role/permissions)
 */
this.constraintProfiles = {
    registration: "email,password,confirmPassword,firstName,lastName",
    login: "email,password",
    profileUpdate: "firstName,lastName,bio,phone,address",
    passwordChange: "currentPassword,password,confirmPassword",
    adminEdit: "firstName,lastName,email,role,permissions,active"
};
```

### 4. Validate Profile Efficiency

Monitor which profiles are used most frequently and optimize accordingly:

```javascript
// High-frequency profiles should be as lean as possible
frequentUpdate: "firstName,lastName"  // 2 fields - very fast

// Infrequent but comprehensive profiles can be larger
completeRegistration: "firstName,lastName,email,password,confirmPassword,phone,address,preferences"  // 8 fields - acceptable for rare use
```

## See Also

- [Validating Constraints](README.md) - Main validation guide
- [A-la-carte Constraints](validating-with-a-la-carte-constraints.md) - Dynamic constraint definition
- [Shared Constraints](validating-with-shared-constraints.md) - Reusable constraint sets
- [Custom Validators](../../advanced/advanced-custom-validators.md) - Building custom validation logic
