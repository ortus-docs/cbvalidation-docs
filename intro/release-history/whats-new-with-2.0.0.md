# What's New With 2.0.0

## Features

* No more manual discovery of validators, automated registration and lookup process, cleaned lots of code on this one!
* New Validator: `Accepted` - The field under validation must be yes, on, 1, or true. This is useful for validating "Terms of Service" acceptance.
* New Validator: `Alpha` - Only allows alphabetic characters
* New Validator: `RequiredUnless` with validation data as a struct literal `{ anotherField:value, ... }`  -  The field under validation must be present and not empty unless the `anotherfield` field is equal to the passed `value`.
* New Validator: `RequiredIf` with validation data as a struct literal `{ anotherField:value, ... }`  -  The field under validation must be present and not empty if the `anotherfield` field is equal to the passed `value`.
* Accelerated validation by removing type checks. ACF chokes on interface checks

## Improvements

* Consistency on all validators to ignore null or empty values except the `Required` validator
* Formatting consistencies
* Improve error messages to describe better validation
* Get away from `evaluate()` instead use `invoke()`

## Compat & Bugs

* `Bugs` : Fixed lots of wrong type exceptions
* `Compat` : Remove ACF11 support

