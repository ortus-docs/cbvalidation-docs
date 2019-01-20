# i18n Integration

## Internationalization

If you are using i18n \(Internationalization and Localization\) in your ColdBox applications you can also localize your validation error messages from the ColdBox validators.

{% hint style="info" %}
 **Info** You do not need to install the `cbi18n` module. This module is already a dependency of the `cbvalidation` module.
{% endhint %}

 You will do this by our lovely conventions for you resource bundle keys:

###  Objects:

```text
{ObjectName}.{Field}.{ConstraintType}}=Message
```

###  Forms with Shared Constraints Name

```text
{SharedConstraintName}.{Field}.{ConstraintType}=Message
```

###  Forms with No Shared Constraints

```text
GenericForm.{Field}.{ConstraintType}=Message
```

### Key Replacements

We also setup lots of global `{Key}` replacements for your messages and also several that the core constraint validators offer as well:

#### Global Replacements

* `{rejectedValue}` - The rejected value
* `{field}` or property - The property or field that was validated
* `{validationType}` - The name of the constraint validator
* `{validationData}` - The value of the constraint definition, e.g size=5..10, then this value is 5..10
* `{targetName}` - The name of the user, shared constraint or form

#### i18n Validator Replacements

* `{DiscreteValidator}` - operation, operationValue
* `{InListValidator}` - inList
* `{MaxValidator}` - max
* `{MinValidator}` - min
* `{RangeValidator}` - range, min, max
* `{RegexValidator}` - regex
* `{SameAsValidator}`, `{SameAsNoCaseValidator}` - SameAs
* `{SizeValidator}` - size, min, max
* `{TypeValidator}` - type

#### **Examples**

```text
blank=The field {property} must contain a value.
email=The field {property} is not a valid email address.
unique=The field {property} is not a unique value.
size=The field {property} was not in the size range of {size}.
inlist=The field {property} was not in the list of possible values.
validator=There was a problem with {property}.
min=The minimum value {min} was not met for the field {property}.
max=The maximum value {max} was exceeded for the field {property}.
range=The range was not met for the field {property}.
matches=The field {property} does not match {regex}.
numeric=The field {property} is not a valid number.
```



