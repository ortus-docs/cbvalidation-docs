# Domain Object Constraints

Domain object constraints are the most powerful and maintainable way to define validation rules in CBValidation. By declaring constraints directly within your model objects, you create self-validating entities that encapsulate both data structure and validation logic.

## Overview

Within any domain object (model, entity, or component), you can define a public variable called `this.constraints` that contains validation rules for your object's properties. This approach provides:

- **Encapsulation**: Validation rules live with the data they validate
- **Reusability**: Objects carry their validation wherever they're used
- **Maintainability**: Single source of truth for validation logic
- **IDE Support**: Auto-completion and syntax highlighting for constraints

## Basic Declaration

{% tabs %}

{% tab title="BoxLang" %}

{% code title="models/User.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
class {

    // Object properties
    property id;
    property fname;
    property lname;
    property email;
    property username;
    property password;
    property age;

    // Validation constraints
    this.constraints = {
        // Constraints go here
    };
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="models/User.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
component persistent="true" {

    // Object properties
    property name="id" fieldtype="id" generator="native" setter="false";
    property name="fname";
    property name="lname";
    property name="email";
    property name="username";
    property name="password";
    property name="age";

    // Validation constraints
    this.constraints = {
        // Constraints go here
    };
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

## Complete Constraint Definitions

Here's a comprehensive example showing various constraint types and validation rules:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="models/User.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
class {

    property id;
    property fname;
    property lname;
    property email;
    property username;
    property password;
    property age;
    property phone;
    property website;
    property birthDate;

    // Comprehensive validation constraints
    this.constraints = {
        fname = {
            required = true,
            requiredMessage = "First name is required",
            size = "2..50",
            sizeMessage = "First name must be 2-50 characters"
        },

        lname = {
            required = true,
            requiredMessage = "Last name is required",
            size = "2..50",
            sizeMessage = "Last name must be 2-50 characters"
        },

        username = {
            required = true,
            size = "6..20",
            regex = "^[a-zA-Z0-9_]+$",
            regexMessage = "Username can only contain letters, numbers, and underscores"
        },

        password = {
            required = true,
            size = "8..128",
            sizeMessage = "Password must be at least 8 characters long"
        },

        email = {
            required = true,
            type = "email",
            typeMessage = "Please enter a valid email address"
        },

        age = {
            required = true,
            type = "numeric",
            range = "13..120",
            rangeMessage = "Age must be between 13 and 120"
        },

        phone = {
            regex = "^\d{3}-\d{3}-\d{4}$",
            regexMessage = "Phone must be in format: 123-456-7890"
        },

        website = {
            type = "url",
            typeMessage = "Please enter a valid website URL"
        },

        birthDate = {
            type = "date",
            before = "#now()#",
            beforeMessage = "Birth date must be in the past"
        }
    };
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="models/User.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
component persistent="true" {

    property name="id" fieldtype="id" generator="native" setter="false";
    property name="fname";
    property name="lname";
    property name="email";
    property name="username";
    property name="password";
    property name="age";
    property name="phone";
    property name="website";
    property name="birthDate";

    // Comprehensive validation constraints
    this.constraints = {
        fname = {
            required = true,
            requiredMessage = "First name is required",
            size = "2..50",
            sizeMessage = "First name must be 2-50 characters"
        },

        lname = {
            required = true,
            requiredMessage = "Last name is required",
            size = "2..50",
            sizeMessage = "Last name must be 2-50 characters"
        },

        username = {
            required = true,
            size = "6..20",
            regex = "^[a-zA-Z0-9_]+$",
            regexMessage = "Username can only contain letters, numbers, and underscores"
        },

        password = {
            required = true,
            size = "8..128",
            sizeMessage = "Password must be at least 8 characters long"
        },

        email = {
            required = true,
            type = "email",
            typeMessage = "Please enter a valid email address"
        },

        age = {
            required = true,
            type = "numeric",
            range = "13..120",
            rangeMessage = "Age must be between 13 and 120"
        },

        phone = {
            regex = "^\d{3}-\d{3}-\d{4}$",
            regexMessage = "Phone must be in format: 123-456-7890"
        },

        website = {
            type = "url",
            typeMessage = "Please enter a valid website URL"
        },

        birthDate = {
            type = "date",
            before = "#now()#",
            beforeMessage = "Birth date must be in the past"
        }
    };
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

{% hint style="info" %}
By default all properties are of type **string** and **not** required. Always explicitly define your constraints for clarity and maintainability.
{% endhint %}

## Constraint Profiles for Targeted Validation

Use constraint profiles to validate only specific fields for different scenarios:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="models/User.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
class {

    // Properties...

    this.constraints = {
        // ... constraint definitions ...
    };

    // Constraint profiles for different validation scenarios
    this.constraintProfiles = {
        registration = "fname,lname,email,username,password",
        profileUpdate = "fname,lname,email,phone,website",
        login = "username,password",
        passwordReset = "email"
    };
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="models/User.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
component {

    // Properties...

    this.constraints = {
        // ... constraint definitions ...
    };

    // Constraint profiles for different validation scenarios
    this.constraintProfiles = {
        registration = "fname,lname,email,username,password",
        profileUpdate = "fname,lname,email,phone,website",
        login = "username,password",
        passwordReset = "email"
    };
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

## Usage in Your Application

### Basic Validation

Once constraints are defined, you can validate objects directly:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="handlers/UserHandler.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
function register( event, rc, prc ) {
    var user = populateModel( "User" );

    // Validate the entire user object
    var result = validate( user );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "users/register" );
    }

    // Save valid user
    userService.save( user );

    event.setView( "users/success" );
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="handlers/UserHandler.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
function register( event, rc, prc ) {
    var user = populateModel( "User" );

    // Validate the entire user object
    var result = validate( user );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "users/register" );
    }

    // Save valid user
    userService.save( user );

    event.setView( "users/success" );
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

### Profile-Based Validation

Use constraint profiles for specific validation scenarios:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="handlers/UserHandler.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
function updateProfile( event, rc, prc ) {
    var user = userService.get( rc.id );
    populateModel( user );

    // Only validate profile update fields
    var result = validate( target=user, profiles="profileUpdate" );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "users/profile" );
    }

    userService.update( user );

    flash.put( "notice", "Profile updated successfully" );
    event.setView( "users/profile" );
}

function authenticate( event, rc, prc ) {
    var user = populateModel( "User" );

    // Only validate login credentials
    var result = validate( target=user, profiles="login" );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "auth/login" );
    }

    // Proceed with authentication
    authService.authenticate( user );
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="handlers/UserHandler.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
function updateProfile( event, rc, prc ) {
    var user = userService.get( rc.id );
    populateModel( user );

    // Only validate profile update fields
    var result = validate( target=user, profiles="profileUpdate" );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "users/profile" );
    }

    userService.update( user );

    flash.put( "notice", "Profile updated successfully" );
    event.setView( "users/profile" );
}

function authenticate( event, rc, prc ) {
    var user = populateModel( "User" );

    // Only validate login credentials
    var result = validate( target=user, profiles="login" );

    if ( result.hasErrors() ) {
        prc.errors = result.getAllErrors();
        return event.setView( "auth/login" );
    }

    // Proceed with authentication
    authService.authenticate( user );
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

### Exception-Based Validation

For APIs or scenarios where you want validation failures to throw exceptions:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="handlers/API/UserHandler.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
function create( event, rc, prc ) {
    try {
        var user = populateModel( "User" );

        // Throws ValidationException if validation fails
        validateOrFail( target=user, profiles="registration" );

        var savedUser = userService.save( user );

        return event.renderData(
            statusCode=201,
            data={ "user": savedUser, "message": "User created successfully" }
        );

    } catch( ValidationException e ) {
        return event.renderData(
            statusCode=422,
            data={ "errors": deserializeJSON( e.extendedInfo ) }
        );
    }
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="handlers/API/UserHandler.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
function create( event, rc, prc ) {
    try {
        var user = populateModel( "User" );

        // Throws ValidationException if validation fails
        validateOrFail( target=user, profiles="registration" );

        var savedUser = userService.save( user );

        return event.renderData(
            statusCode=201,
            data={ "user": savedUser, "message": "User created successfully" }
        );

    } catch( ValidationException e ) {
        return event.renderData(
            statusCode=422,
            data={ "errors": deserializeJSON( e.extendedInfo ) }
        );
    }
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

## Advanced Domain Object Examples

### Complex Nested Objects

Domain objects can contain complex nested structures:

{% tabs %}

{% tab title="BoxLang" %}

{% code title="models/Employee.bx" overflow="wrap" lineNumbers="true" %}

{% code language="groovy" %}
class {

    property id;
    property firstName;
    property lastName;
    property email;
    property department;
    property salary;
    property hireDate;
    property address;
    property phoneNumbers;

    this.constraints = {
        firstName = { required=true, size="2..50" },
        lastName = { required=true, size="2..50" },
        email = { required=true, type="email" },
        department = { required=true, inList="Engineering,Sales,Marketing,HR" },
        salary = { required=true, type="numeric", min="0" },
        hireDate = { required=true, type="date", before="#now()#" },

        // Nested address validation
        "address.street" = { required=true, size="5..100" },
        "address.city" = { required=true, size="2..50" },
        "address.state" = { required=true, size="2" },
        "address.zip" = { required=true, regex="^\d{5}(-\d{4})?$" },

        // Array validation for phone numbers
        "phoneNumbers.*.type" = { required=true, inList="mobile,home,work" },
        "phoneNumbers.*.number" = { required=true, regex="^\d{3}-\d{3}-\d{4}$" }
    };

    this.constraintProfiles = {
        basic = "firstName,lastName,email",
        complete = "firstName,lastName,email,department,salary,hireDate",
        contact = "firstName,lastName,email,phoneNumbers.*"
    };
}
{% endcode %}

{% endcode %}

{% endtab %}

{% tab title="CFML" %}

{% code title="models/Employee.cfc" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
component {

    property name="id";
    property name="firstName";
    property name="lastName";
    property name="email";
    property name="department";
    property name="salary";
    property name="hireDate";
    property name="address";
    property name="phoneNumbers";

    this.constraints = {
        firstName = { required=true, size="2..50" },
        lastName = { required=true, size="2..50" },
        email = { required=true, type="email" },
        department = { required=true, inList="Engineering,Sales,Marketing,HR" },
        salary = { required=true, type="numeric", min="0" },
        hireDate = { required=true, type="date", before="#now()#" },

        // Nested address validation
        "address.street" = { required=true, size="5..100" },
        "address.city" = { required=true, size="2..50" },
        "address.state" = { required=true, size="2" },
        "address.zip" = { required=true, regex="^\d{5}(-\d{4})?$" },

        // Array validation for phone numbers
        "phoneNumbers.*.type" = { required=true, inList="mobile,home,work" },
        "phoneNumbers.*.number" = { required=true, regex="^\d{3}-\d{3}-\d{4}$" }
    };

    this.constraintProfiles = {
        basic = "firstName,lastName,email",
        complete = "firstName,lastName,email,department,salary,hireDate",
        contact = "firstName,lastName,email,phoneNumbers.*"
    };
}
{% endcode %}

{% endcode %}

{% endtab %}

{% endtabs %}

## Best Practices

### 1. **Use Meaningful Constraint Names**

Match constraint field names exactly to your object properties for clarity and maintainability.

### 2. **Provide Custom Messages**

Always include custom error messages that are user-friendly and actionable.

### 3. **Leverage Constraint Profiles**

Use profiles to validate only relevant fields for different business scenarios.

### 4. **Group Related Constraints**

Organize constraints logically within your object definition for better readability.

### 5. **Handle Nested Data**

Use dot notation and asterisk wildcards for complex data structures.

### 6. **Validate Early and Often**

Validate at the domain object level to catch issues before they propagate through your application.

Domain object constraints provide the most maintainable and reusable approach to validation in CBValidation, ensuring your business objects are always self-validating and consistent across your entire application.
