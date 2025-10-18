# A-la-carte Constraint Validation

A-la-carte validation allows you to define constraints dynamically within your event handlers, providing maximum flexibility for validation scenarios that don't warrant permanent constraint definitions. This approach is perfect for:

- **API endpoints** with specific validation requirements
- **Form processing** with dynamic validation rules
- **Data import/export** operations with varying formats
- **Administrative functions** with custom validation logic
- **Temporary or one-off validation** scenarios

## Overview

With a-la-carte validation, you can:

- Create constraints on-the-fly within handler methods
- Store constraint definitions in databases, JSON files, or services
- Dynamically build validation rules based on business logic
- Validate any struct, object, or request collection
- Control exactly which fields to validate using inclusion/exclusion parameters

## Basic A-la-carte Validation

### Simple Request Collection Validation

{% tabs %}

{% tab title="BoxLang" %}

{% code title="handlers/UserHandler.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
function create( event, rc, prc ) {
    // Define constraints inline for this specific endpoint
    var validationResult = validate(
        target = rc,
        constraints = {
            username = { required = true, size = "3..20" },
            email = { required = true, type = "email" },
            password = { required = true, size = "8..128" },
            confirmPassword = { required = true, sameAs = "password" }
        }
    );

    if ( !validationResult.hasErrors() ) {
        var newUser = userService.createUser(
            username = rc.username,
            email = rc.email,
            password = rc.password
        );

        prc.response.setData( newUser );
    } else {
        prc.response
            .setError( true )
            .addMessage( validationResult.getAllErrors() )
            .setStatusCode( 400 )
            .setStatusText( "Validation failed" );
    }
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="handlers/UserHandler.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
function create( event, rc, prc ) {
    // Define constraints inline for this specific endpoint
    var validationResult = validate(
        target = rc,
        constraints = {
            username = { required = true, size = "3..20" },
            email = { required = true, type = "email" },
            password = { required = true, size = "8..128" },
            confirmPassword = { required = true, sameAs = "password" }
        }
    );

    if ( !validationResult.hasErrors() ) {
        var newUser = userService.createUser(
            username = rc.username,
            email = rc.email,
            password = rc.password
        );

        prc.response.setData( newUser );
    } else {
        prc.response
            .setError( true )
            .addMessage( validationResult.getAllErrors() )
            .setStatusCode( 400 )
            .setStatusText( "Validation failed" );
    }
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

### Exception-Based Validation

For cleaner API code, use `validateOrFail()` to automatically throw exceptions on validation failure:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="handlers/API/UserHandler.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
function register( event, rc, prc ) {
    try {
        // Throws ValidationException if validation fails
        validateOrFail(
            target = rc,
            constraints = {
                firstName = { required = true, size = "2..50" },
                lastName = { required = true, size = "2..50" },
                email = { required = true, type = "email" },
                password = { required = true, size = "8..128" },
                termsAccepted = { accepted = true }
            }
        );

        var user = userService.register( rc );

        return event.renderData(
            statusCode = 201,
            data = { user = user, message = "Registration successful" }
        );

    } catch( ValidationException e ) {
        return event.renderData(
            statusCode = 422,
            data = { errors = deserializeJSON( e.extendedInfo ) }
        );
    }
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="handlers/API/UserHandler.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
function register( event, rc, prc ) {
    try {
        // Throws ValidationException if validation fails
        validateOrFail(
            target = rc,
            constraints = {
                firstName = { required = true, size = "2..50" },
                lastName = { required = true, size = "2..50" },
                email = { required = true, type = "email" },
                password = { required = true, size = "8..128" },
                termsAccepted = { accepted = true }
            }
        );

        var user = userService.register( rc );

        return event.renderData(
            statusCode = 201,
            data = { user = user, message = "Registration successful" }
        );

    } catch( ValidationException e ) {
        return event.renderData(
            statusCode = 422,
            data = { errors = deserializeJSON( e.extendedInfo ) }
        );
    }
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

## Field Inclusion and Exclusion

Control exactly which fields get validated using the `includeFields` and `excludeFields` parameters:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="handlers/UserHandler.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
function updateProfile( event, rc, prc ) {
    var constraints = {
        firstName = { required = true, size = "2..50" },
        lastName = { required = true, size = "2..50" },
        email = { required = true, type = "email" },
        phone = { regex = "^\d{3}-\d{3}-\d{4}$" },
        website = { type = "url" },
        password = { size = "8..128" },  // Optional password change
        currentPassword = { required = false } // Only if password changing
    };

    var fieldsToValidate = "firstName,lastName,email,phone,website";

    // Only validate profile fields, skip password-related fields
    var result = validate(
        target = rc,
        constraints = constraints,
        includeFields = fieldsToValidate
    );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "users/profile" );
    }

    userService.updateProfile( rc );
    flash.put( "success", "Profile updated successfully" );

    return event.setView( "users/profile" );
}

function changePassword( event, rc, prc ) {
    var constraints = {
        currentPassword = { required = true },
        newPassword = { required = true, size = "8..128" },
        confirmPassword = { required = true, sameAs = "newPassword" }
    };

    // Only validate password-related fields
    var result = validate(
        target = rc,
        constraints = constraints,
        includeFields = "currentPassword,newPassword,confirmPassword"
    );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "users/changePassword" );
    }

    userService.changePassword( rc );
    flash.put( "success", "Password changed successfully" );

    return event.setView( "users/profile" );
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="handlers/UserHandler.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
function updateProfile( event, rc, prc ) {
    var constraints = {
        firstName = { required = true, size = "2..50" },
        lastName = { required = true, size = "2..50" },
        email = { required = true, type = "email" },
        phone = { regex = "^\d{3}-\d{3}-\d{4}$" },
        website = { type = "url" },
        password = { size = "8..128" },  // Optional password change
        currentPassword = { required = false } // Only if password changing
    };

    var fieldsToValidate = "firstName,lastName,email,phone,website";

    // Only validate profile fields, skip password-related fields
    var result = validate(
        target = rc,
        constraints = constraints,
        includeFields = fieldsToValidate
    );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "users/profile" );
    }

    userService.updateProfile( rc );
    flash.put( "success", "Profile updated successfully" );

    return event.setView( "users/profile" );
}

function changePassword( event, rc, prc ) {
    var constraints = {
        currentPassword = { required = true },
        newPassword = { required = true, size = "8..128" },
        confirmPassword = { required = true, sameAs = "newPassword" }
    };

    // Only validate password-related fields
    var result = validate(
        target = rc,
        constraints = constraints,
        includeFields = "currentPassword,newPassword,confirmPassword"
    );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "users/changePassword" );
    }

    userService.changePassword( rc );
    flash.put( "success", "Password changed successfully" );

    return event.setView( "users/profile" );
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

## Dynamic Constraint Building

Build constraints programmatically based on business logic or user roles:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="handlers/AdminHandler.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
function createUser( event, rc, prc ) {
    // Base constraints for all users
    var constraints = {
        username = { required = true, size = "3..20" },
        email = { required = true, type = "email" },
        firstName = { required = true, size = "2..50" },
        lastName = { required = true, size = "2..50" }
    };

    // Add role-specific constraints
    if ( rc.role == "admin" ) {
        constraints.adminCode = { required = true, size = "10" };
        constraints.permissions = { required = true, type = "array" };
    }

    if ( rc.role == "employee" ) {
        constraints.department = { required = true, inList = "HR,IT,Sales,Marketing" };
        constraints.employeeId = { required = true, regex = "^EMP\d{6}$" };
    }

    // Add password constraints if creating new account
    if ( !structKeyExists( rc, "userId" ) ) {
        constraints.password = { required = true, size = "8..128" };
        constraints.confirmPassword = { required = true, sameAs = "password" };
    }

    var result = validate( target = rc, constraints = constraints );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "admin/users/form" );
    }

    var user = userService.createUser( rc );

    flash.put( "success", "User created successfully" );
    return event.relocate( "admin.users.index" );
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="handlers/AdminHandler.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
function createUser( event, rc, prc ) {
    // Base constraints for all users
    var constraints = {
        username = { required = true, size = "3..20" },
        email = { required = true, type = "email" },
        firstName = { required = true, size = "2..50" },
        lastName = { required = true, size = "2..50" }
    };

    // Add role-specific constraints
    if ( rc.role == "admin" ) {
        constraints.adminCode = { required = true, size = "10" };
        constraints.permissions = { required = true, type = "array" };
    }

    if ( rc.role == "employee" ) {
        constraints.department = { required = true, inList = "HR,IT,Sales,Marketing" };
        constraints.employeeId = { required = true, regex = "^EMP\d{6}$" };
    }

    // Add password constraints if creating new account
    if ( !structKeyExists( rc, "userId" ) ) {
        constraints.password = { required = true, size = "8..128" };
        constraints.confirmPassword = { required = true, sameAs = "password" };
    }

    var result = validate( target = rc, constraints = constraints );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "admin/users/form" );
    }

    var user = userService.createUser( rc );

    flash.put( "success", "User created successfully" );
    return event.relocate( "admin.users.index" );
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

## External Constraint Sources

Load constraints from external sources like databases, JSON files, or services:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="handlers/FormHandler.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
function processCustomForm( event, rc, prc ) {
    // Load constraints from database or JSON configuration
    var formConfig = formService.getFormConfiguration( rc.formId );
    var constraints = formConfig.validationRules;

    var result = validate( target = rc, constraints = constraints );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        prc.form = formConfig;
        return event.setView( "forms/custom" );
    }

    // Process valid form data
    formService.saveFormSubmission( rc.formId, rc );

    flash.put( "success", "Form submitted successfully" );
    return event.relocate( "forms.thankyou" );
}

// Example service method to load constraints
// FormService.bx
function getFormConfiguration( formId ) {
    // Could load from database, JSON file, etc.
    var formData = query(
        sql = "SELECT validation_rules FROM custom_forms WHERE id = ?",
        params = [ formId ]
    );

    return {
        validationRules = deserializeJSON( formData.validation_rules[1] ),
        fields = getFormFields( formId )
    };
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="handlers/FormHandler.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
function processCustomForm( event, rc, prc ) {
    // Load constraints from database or JSON configuration
    var formConfig = formService.getFormConfiguration( rc.formId );
    var constraints = formConfig.validationRules;

    var result = validate( target = rc, constraints = constraints );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        prc.form = formConfig;
        return event.setView( "forms/custom" );
    }

    // Process valid form data
    formService.saveFormSubmission( rc.formId, rc );

    flash.put( "success", "Form submitted successfully" );
    return event.relocate( "forms.thankyou" );
}

// Example service method to load constraints
// FormService.cfc
function getFormConfiguration( formId ) {
    // Could load from database, JSON file, etc.
    var formData = queryExecute(
        sql = "SELECT validation_rules FROM custom_forms WHERE id = ?",
        params = [ formId ]
    );

    return {
        validationRules = deserializeJSON( formData.validation_rules[1] ),
        fields = getFormFields( formId )
    };
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

## Complex Data Structure Validation

Validate nested objects and arrays using a-la-carte constraints:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="handlers/OrderHandler.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
function processOrder( event, rc, prc ) {
    var constraints = {
        // Customer information
        "customer.name" = { required = true, size = "2..100" },
        "customer.email" = { required = true, type = "email" },
        "customer.phone" = { regex = "^\d{3}-\d{3}-\d{4}$" },

        // Shipping address
        "shipping.street" = { required = true, size = "5..200" },
        "shipping.city" = { required = true, size = "2..100" },
        "shipping.state" = { required = true, size = "2" },
        "shipping.zip" = { required = true, regex = "^\d{5}(-\d{4})?$" },

        // Order items (array validation)
        "items.*.productId" = { required = true, type = "string" },
        "items.*.quantity" = { required = true, type = "numeric", min = "1" },
        "items.*.price" = { required = true, type = "numeric", min = "0.01" },

        // Payment information
        "payment.method" = { required = true, inList = "credit,debit,paypal" },
        "payment.total" = { required = true, type = "numeric", min = "0.01" }
    };

    var result = validate( target = rc, constraints = constraints );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "orders/checkout" );
    }

    var order = orderService.processOrder( rc );

    return event.renderData(
        statusCode = 201,
        data = { orderId = order.id, message = "Order processed successfully" }
    );
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="handlers/OrderHandler.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
function processOrder( event, rc, prc ) {
    var constraints = {
        // Customer information
        "customer.name" = { required = true, size = "2..100" },
        "customer.email" = { required = true, type = "email" },
        "customer.phone" = { regex = "^\d{3}-\d{3}-\d{4}$" },

        // Shipping address
        "shipping.street" = { required = true, size = "5..200" },
        "shipping.city" = { required = true, size = "2..100" },
        "shipping.state" = { required = true, size = "2" },
        "shipping.zip" = { required = true, regex = "^\d{5}(-\d{4})?$" },

        // Order items (array validation)
        "items.*.productId" = { required = true, type = "string" },
        "items.*.quantity" = { required = true, type = "numeric", min = "1" },
        "items.*.price" = { required = true, type = "numeric", min = "0.01" },

        // Payment information
        "payment.method" = { required = true, inList = "credit,debit,paypal" },
        "payment.total" = { required = true, type = "numeric", min = "0.01" }
    };

    var result = validate( target = rc, constraints = constraints );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "orders/checkout" );
    }

    var order = orderService.processOrder( rc );

    return event.renderData(
        statusCode = 201,
        data = { orderId = order.id, message = "Order processed successfully" }
    );
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

## Best Practices

### 1. **Use for Specific Scenarios**

A-la-carte validation is best for endpoint-specific requirements, not general business rules.

### 2. **Keep Constraints Close to Usage**

Define constraints within the handler method for better maintainability and context.

### 3. **Leverage Field Control Parameters**

Use `includeFields` and `excludeFields` to validate only relevant data.

### 4. **Handle Complex Data Structures**

Use dot notation and asterisk wildcards for nested objects and arrays.

### 5. **Consider External Sources**

For dynamic forms or configurable validation, load constraints from databases or configuration files.

### 6. **Provide Meaningful Error Messages**

Include custom error messages that guide users toward resolution.

A-la-carte validation provides the flexibility to handle unique validation scenarios while maintaining the power and consistency of CBValidation's constraint system. It's perfect for API endpoints, dynamic forms, and any situation where domain object constraints aren't sufficient or appropriate.
