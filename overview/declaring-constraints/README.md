# Declaring Constraints

## What are Constraints?

A constraint is by definition the following:

> The state of being restricted or confined within prescribed bounds.

That is exactly what you will create for specific fields. You will declare the constraints for one or more fields.  Each constraint will be composed of one or more **validators** and **validation data**.  The validation data is defined by the validator and can be of `any` type, the default is an empty struct \(`{}`\)

```javascript
// Define the field by name
// The contents are the constraints
fieldName1 = {
    validator1 = validationData,
    validator2 = validationData
},

fieldName2 = {
    validator1 = validationData,
    validator2 = validationData
}
```

These constraints can then be defined in many locations where cbValidation can read them.

## Defining Constraints

You can define constraints in several locations:

1. [Configuration file](configuration-file.md)
2. [Inside a domain object](domain-object.md)
3. [A-la-carte](a-la-carte-via-event-handlers.md)

{% hint style="info" %}
When validating using  `validate(), validateOrFail()` you have to specify a **target**, but specifying a **constraint** in your call is optional.
{% endhint %}

## Constraints Discovery

When you call the validation methods with **NO** `constraints` passed explicitly, then the validation module will discover the constraints using the following:

* Lookup your constraints in `myTarget.constraints` struct in your target object or struct.
* If you specify your constraint parameter as a **string**, the validator will lookup a shared constraint in your configuration file definitions.
* If you specify your constraint parameter as a **struct**, this struct will directly server as your set of constraints, so you can specify your constraints on the fly,  or specify an alternative set of constraints in your model, e.g `User.constraints` vs `User.signInConstraints`

## 

