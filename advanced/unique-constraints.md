# Unique ORM Validator

## Usage

The `unique` validator is part of the [cborm](https://github.com/coldbox/cbox-cborm) module. So make sure that the `cborm` module is installed first.

```bash
box install cborm
```

## Declaring the Constraint

The validator is mapped into WireBox as `UniqueValidator@cborm` so you can use in your constraints like so:

```javascript
{ 
    fieldName : { validator: "UniqueValidator@cborm" },
    // or
    fieldName : { "UniqueValidator@cborm" : {}  }
}
```

## Case Sensitivity

If you will be using this validator, then the name of the property has to be **EXACTLY** the same case as the constraint name. To do this, use single or double quotes to declare the constraint name. Please see example below.

```javascript
this.constraints = {
  "username" = { required=true, validator: "UniqueValidator@cborm" },
  "email" = { required=true, validator: "UniqueValidator@cborm" }
};
```

{% hint style="info" %}
This is done because we build the appropriate SQL to make sure the property name and the field name match.
{% endhint %}

