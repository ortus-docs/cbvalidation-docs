# Unique Constraints

## Usage

The `unique` constraint is part of the [cborm](https://github.com/coldbox/cbox-cborm) module. So make sure that the `cborm` module is installed first.

```bash
box install cborm
```

{% hint style="info" %}
See the [Advanced Custom Validators](../custom-validators/advanced-custom-validators.md) for a uniqueness validator which is **not** dependent of ORM
{% endhint %}

## Declaring the Constraint

The constraints is mapped into wirebox as `UniqueValidator@cborm` so you can use in your constraints like so:

```javascript
{ fieldName : { validator: "UniqueValidator@cborm" } }
```

## Case Sensitivity

If you will be using the unique constraint, then the name of the property has to be **EXACTLY** the same case as the constraint name. To do this, use single or double quotes to declare the constraint name. Please see example below.

```javascript
this.constraints = {
  "username" = { required=true, validator: "UniqueValidator@cborm" },
  "email" = { required=true, validator: "UniqueValidator@cborm" }
};
```

