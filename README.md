---
description: cbValidation is the server-side validation engine for ColdBox applications
icon: sunglasses
---

CBValidation is the server-side validation engine that provides a unified approach to object, struct, and form validation for ColdBox applications. Built with flexibility and performance in mind, it allows you to construct declarative validation constraint rules and validate them with ease. Whether you're validating API requests, form submissions, or domain objects, CBValidation gives you the tools to ensure data integrity across your entire application.

The validation engine is based on **constraint-driven validation** where you declaratively specify validation rules for properties or fields. These constraints can live directly in your domain objects, be defined as shared constraints in your ColdBox configuration, or created dynamically on-the-fly for maximum flexibility.

## Quick Example 🚀

Here's a taste of CBValidation's power with a simple user registration example:

{% code title="models/User.bx" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
class {
    property name="firstName";
    property name="lastName";
    property name="email";
    property name="password";
    property name="age";

    // Define validation constraints directly in your model
    this.constraints = {
        firstName = {
            required = true,
            size = "2..50",
            requiredMessage = "Please enter your first name"
        },
        lastName = {
            required = true,
            size = "2..50",
            requiredMessage = "Please enter your last name"
        },
        email = {
            required = true,
            type = "email",
            typeMessage = "Please enter a valid email address"
        },
        password = {
            required = true,
            size = "8..128",
            sizeMessage = "Password must be at least 8 characters long"
        },
        age = {
            required = true,
            type = "numeric",
            range = "13..120",
            rangeMessage = "Age must be between 13 and 120"
        }
    };
}
{% endcode %}

{% endcode %}

{% code title="handlers/UserHandler.bx" overflow="wrap" lineNumbers="true" %}

{% code language="javascript" %}
function register( event, rc, prc ) {
    validate( populate( "User" ) )
        .onError( results => {
            flash.put( "errors", results.getAllErrors() );
            relocate( "users/editor" );
        })
        .onSuccess( results => {
            userService.save( user );
            flash.put( "success", "Registration successful!" );
            relocate( "users/welcome" );
        });
}
{% endcode %}

{% endcode %}

With just two simple validation methods - `validate()` and `validateOrFail()` - you get comprehensive validation with detailed error reporting, custom messages, and seamless integration with your ColdBox application.

## Features ✨

### 🎯 **Flexible Constraint Definition**

- **Domain Object Constraints** - Define validation rules directly in your models for encapsulated validation logic
- **Shared Constraints** - Reusable validation rules stored in your ColdBox configuration
- **A-la-carte Constraints** - Dynamic validation rules created on-the-fly for specific scenarios
- **External Constraints** - Load validation rules from databases, JSON files, or services

### 🔧 **Powerful Validation Rules**

- **30+ Built-in Validators** - Required, email, size, range, regex, date comparisons, and more
- **Custom Validators** - Create your own validation rules with full framework integration
- **Nested Object Support** - Validate complex data structures with dot notation (`user.address.street`)
- **Array Validation** - Validate arrays and array items with wildcard notation (`items.*.price`)
- **Conditional Validation** - Rules like `requiredIf`, `requiredUnless` for dynamic requirements

### 🎨 **User Experience Features**

- **Custom Error Messages** - Personalized, user-friendly validation messages
- **Message Replacement Variables** - Dynamic messages with context like `{min}`, `{max}`, `{rejectedValue}`
- **Internationalization (i18n)** - Multi-language validation messages through cbi18n integration
- **Constraint Profiles** - Validate specific field groups for different scenarios (registration, update, etc.)

### 🚀 **Developer Experience**

- **Two Simple Methods** - `validate()` for result objects, `validateOrFail()` for exception-based validation
- **Rich Error Information** - Detailed validation results with field names, messages, and metadata
- **Exception Integration** - Automatic ValidationException throwing with JSON error details
- **Mixin Integration** - Validation methods available globally in handlers, views, layouts, and interceptors

### 🔄 **Advanced Features**

- **Method/UDF Validation** - Call custom validation methods with error metadata support
- **Database Unique Constraints** - Built-in unique field validation against database tables
- **Null Value Handling** - Intelligent handling of null, empty, and undefined values
- **WireBox Integration** - Full dependency injection support for custom validators
- **Performance Optimized** - Efficient constraint processing and validator caching

### 🛡️ **Enterprise Ready**

- **Production Tested** - Battle-tested in countless ColdBox applications
- **Comprehensive Documentation** - Detailed guides, examples, and best practices
- **Professional Support** - Backed by Ortus Solutions with commercial support options
- **BoxLang Compatible** - Full support for both CFML and BoxLang platforms

## Professional Open Source

![Ortus Solutions, Corp](.gitbook/assets/ortus-solutions-logo.png)

CBValidation is professional open-source software backed by [Ortus Solutions, Corp](https://www.ortussolutions.com/) offering services like:

- Custom Development
- Professional Support & Mentoring
- Training
- Server Tuning
- Security Hardening
- Code Reviews
- [Much More](https://www.ortussolutions.com/)

## Resources & Community

- Source: https://github.com/coldbox-modules/cbvalidation
- Issues: https://github.com/coldbox-modules/cbvalidation/issues
- Official Site: https://www.coldbox.org
- BoxLang Site: https://boxlang.io
- Video Training:
  - https://www.cfcasts.com
  - https://learn.boxlang.io
  - https://youtube.com/c/OrtusSolutions
- Comunity: https://community.ortussolutions.com/
- Slack: https://boxteam.ortussolutions.com

## HONOR GOES TO GOD ABOVE ALL

Because of His grace, this project exists. If you don't like this, then don't read it, it's not for you.

> "Therefore being justified by **faith**, we have peace with God through our Lord Jesus Christ: By whom also we have access by **faith** into this **grace** wherein we stand, and rejoice in hope of the glory of God." Romans 5:5
