# Custom Message Replacements

We also setup lots of global `{Key}` replacements for your messages and also several that the core constraint validators offer as well. This is great for adding these customizations on your custom messages and also your i18n messages \(Keep Reading\):

## Global Replacements

* `{rejectedValue}` - The rejected value
* `{field or property}` - The property or field that was validated
* `{validationType}` - The name of the constraint validator
* `{validationData}` - The value of the constraint definition, e.g size=5..10, then this value is 5..10

## Validator Replacements

* `{DiscreteValidator}` - operation, operationValue
* `{InListValidator}` - inList
* `{MaxValidator}` - max
* `{MinValidator}` - min
* `{RangeValidator}` - range, min, max
* `{RegexValidator}` - regex
* `{SameAsValidator}`, `{SameAsNoCaseValidator}` - sameas
* `{SizeValidator}` - size, min, max
* `{TypeValidator}` - type

```javascript
username = { 
    required="true", 
    requiredMessage="Please enter the {field}", 
    size="6-8", 
    sizeMessage="The username must be between {min} and {max} characters" 
}
```

