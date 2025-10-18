# Constraint Custom Messages

CBValidation provides default English error messages for all constraints, but you can customize these messages to match your application's tone, language, or specific requirements. Custom messages give you complete control over validation error presentation.

## Message Convention

To create a custom message for any constraint, follow this simple pattern:

```javascript
{constraintName}Message = "Your custom error message"
```

The constraint name should match exactly (case-sensitive) with the word "Message" appended.

## Basic Examples

### Single Constraint Messages

```javascript
username = {
    required = true,
    requiredMessage = "Please enter a username"
}
```

### Multiple Constraint Messages

```javascript
username = {
    required = true,
    requiredMessage = "Username is required for your account",
    size = "3..20",
    sizeMessage = "Username must be 3-20 characters long",
    regex = "^[a-zA-Z0-9_]+$",
    regexMessage = "Username can only contain letters, numbers, and underscores"
}
```

## All Constraint Message Options

Here are the message options for all available constraints:

### Core Constraints

```javascript
requiredMessage = "This field is required"
typeMessage = "Please enter a valid value"
sizeMessage = "Value must be the correct size"
emptyMessage = "This field must be empty"
```

### Numeric Constraints

```javascript
minMessage = "Value must be at least the minimum"
maxMessage = "Value cannot exceed the maximum"
rangeMessage = "Value must be within the specified range"
discreteMessage = "Value must meet the numeric criteria"
```

### String Constraints

```javascript
regexMessage = "Value must match the required pattern"
alphaMessage = "Value must contain only letters"
inListMessage = "Please select a valid option"
```

### Comparison Constraints

```javascript
sameAsMessage = "Values must match"
sameAsNoCaseMessage = "Values must match (case-insensitive)"
notSameAsMessage = "Values must be different"
notSameAsNoCaseMessage = "Values must be different (case-insensitive)"
```

### Date Constraints

```javascript
afterMessage = "Date must be after the specified date"
beforeMessage = "Date must be before the specified date"
afterOrEqualMessage = "Date must be on or after the specified date"
beforeOrEqualMessage = "Date must be on or before the specified date"
dateEqualsMessage = "Date must match the specified date"
```

### Advanced Constraints

```javascript
acceptedMessage = "You must accept the terms"
instanceOfMessage = "Value must be of the correct type"
uniqueMessage = "This value already exists"
methodMessage = "Custom validation failed"
udfMessage = "Custom validation failed"
```

## Dynamic Message Replacements

Custom messages support dynamic replacement variables that make your messages more informative:

```javascript
email = {
    required = true,
    requiredMessage = "The {field} field is required",
    type = "email",
    typeMessage = "Please enter a valid {type} in the {field} field"
},

password = {
    size = "8..128",
    sizeMessage = "Password must be {min}-{max} characters (you entered {rejectedValue})"
},

age = {
    range = "18..65",
    rangeMessage = "Age must be between {min} and {max} years (current: {rejectedValue})"
}
```

## User-Friendly Message Examples

### Registration Form

```javascript
this.constraints = {
    firstName = {
        required = true,
        requiredMessage = "Please enter your first name",
        size = "2..50",
        sizeMessage = "First name must be 2-50 characters long"
    },

    email = {
        required = true,
        requiredMessage = "Email address is required",
        type = "email",
        typeMessage = "Please enter a valid email address"
    },

    password = {
        required = true,
        requiredMessage = "Password is required",
        size = "8..128",
        sizeMessage = "Password must be at least 8 characters long"
    },

    confirmPassword = {
        required = true,
        requiredMessage = "Please confirm your password",
        sameAs = "password",
        sameAsMessage = "Password confirmation doesn't match"
    },

    termsAccepted = {
        accepted = true,
        acceptedMessage = "You must accept the terms and conditions"
    }
};
```

### Profile Update Form

```javascript
this.constraints = {
    currentPassword = {
        required = true,
        requiredMessage = "Current password is required to make changes"
    },

    newEmail = {
        type = "email",
        typeMessage = "Please enter a valid email address",
        unique = { table: "users", column: "email" },
        uniqueMessage = "This email address is already registered"
    },

    age = {
        range = "13..120",
        rangeMessage = "Please enter a valid age between 13 and 120"
    }
};
```

## Best Practices

### 1. Be Specific and Helpful

```javascript
// ❌ Vague
requiredMessage = "Required field"

// ✅ Specific and helpful
requiredMessage = "Please enter your phone number so we can contact you"
```

### 2. Use Natural Language

```javascript
// ❌ Technical
sizeMessage = "Length validation failed: min=8, max=20"

// ✅ Natural language
sizeMessage = "Please enter 8-20 characters"
```

### 3. Provide Guidance

```javascript
// ❌ Just states the problem
regexMessage = "Invalid format"

// ✅ Provides guidance
regexMessage = "Phone number should be in format: (555) 123-4567"
```

### 4. Use Replacement Variables

```javascript
// ❌ Static message
rangeMessage = "Value must be between 1 and 100"

// ✅ Dynamic with replacements
rangeMessage = "Please enter a value between {min} and {max}"
```

## i18n Integration

Custom messages work seamlessly with i18n. You can define messages in resource bundles and still use the same constraint message pattern:

```javascript
// In your constraints
email = {
    required = true,
    requiredMessage = "#user.email.required#",
    type = "email",
    typeMessage = "#user.email.invalid#"
}
```

```properties
# In resources/validation_en.properties
user.email.required=Please provide your email address
user.email.invalid=Please enter a valid email address

# In resources/validation_es.properties
user.email.required=Por favor proporciona tu dirección de correo
user.email.invalid=Por favor ingresa una dirección de correo válida
```

This approach allows you to maintain consistent, localized error messages across your entire application while leveraging CBValidation's powerful constraint system.
