# Custom Message Replacements

CBValidation provides powerful message replacement functionality that allows you to create dynamic, context-aware error messages. You can use these replacements in both custom constraint messages and i18n resource bundles.

## Global Replacements

These replacements are available for **all validators**:

* `{rejectedValue}` - The actual value that failed validation
* `{field}` or `{property}` - The name of the field being validated
* `{validationType}` - The name of the constraint validator (e.g., "Required", "Size", "Type")
* `{validationData}` - The constraint's configuration value (e.g., for `size="5..10"`, this would be "5..10")

## Validator-Specific Replacements

Each validator provides additional context-specific replacement variables accessible via the error metadata:

### Numeric Validators

* **`{MaxValidator}`** - `{max}` - The maximum allowed value
* **`{MinValidator}`** - `{min}` - The minimum required value
* **`{RangeValidator}`** - `{range}`, `{min}`, `{max}` - Range boundaries
* **`{DiscreteValidator}`** - `{operation}`, `{operationValue}` - Math operation details

### String & Collection Validators

* **`{SizeValidator}`** - `{size}`, `{min}`, `{max}` - Size constraints for strings, arrays, structs
* **`{TypeValidator}`** - `{type}` - Expected data type
* **`{RegexValidator}`** - `{regex}` - Regular expression pattern
* **`{InListValidator}`** - `{inList}` - Comma-separated list of valid values

### Comparison Validators

* **`{SameAsValidator}`** - `{sameas}` - Field name for comparison
* **`{SameAsNoCaseValidator}`** - `{sameas}` - Field name for case-insensitive comparison
* **`{NotSameAsValidator}`** - `{notsameas}` - Field name that should be different
* **`{NotSameAsNoCaseValidator}`** - `{notsameas}` - Field name for case-insensitive difference check

### Date Validators

* **`{AfterValidator}`** - `{after}` - Reference date or field name
* **`{BeforeValidator}`** - `{before}` - Reference date or field name
* **`{AfterOrEqualValidator}`** - `{afterOrEqual}` - Reference date or field name
* **`{BeforeOrEqualValidator}`** - `{beforeOrEqual}` - Reference date or field name
* **`{DateEqualsValidator}`** - `{dateEquals}` - Reference date or field name

### Database & Uniqueness

* **`{UniqueValidator}`** - `{table}`, `{column}` - Database table and column being checked

## Usage Examples

### Basic Custom Messages

```javascript
username = {
    required = true,
    requiredMessage = "Please enter your {field}",
    size = "3..20",
    sizeMessage = "The {field} must be between {min} and {max} characters"
}
```

### Advanced Examples with Metadata

```javascript
password = {
    required = true,
    size = "8..128",
    sizeMessage = "Password must be at least {min} characters (you entered {rejectedValue} characters)",
    regex = "^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)",
    regexMessage = "Password must contain uppercase, lowercase, and numbers (Pattern: {regex})"
},

age = {
    required = true,
    range = "18..65",
    rangeMessage = "Age must be between {min} and {max} years old (you entered: {rejectedValue})"
},

confirmPassword = {
    required = true,
    sameAs = "password",
    sameAsMessage = "Password confirmation must match your {sameas} field"
}
```

### i18n Integration

You can use these replacements in your resource bundle files:

```properties
# resources/validation_en.properties
user.username.required=Please provide a {field} for your account
user.username.size={field} must be {min}-{max} characters (current: {rejectedValue})
user.age.range=Age must be {min} to {max} years (you entered {rejectedValue})
user.email.type=Please enter a valid {type} address in the {field} field
```

## Error Metadata Access

Starting in CBValidation 4.3.0+, you can access all error metadata programmatically:

```javascript
var result = validate(target=user, constraints="userValidation");
if (result.hasErrors()) {
    for (var error in result.getAllErrors()) {
        var metadata = error.getErrorMetadata();
        // metadata contains all the replacement variables
        // e.g., metadata.min, metadata.max, metadata.type, etc.
    }
}
```

This allows for dynamic error handling and custom error message generation based on the specific validation context.
