# What's New With 3.1.0

### \[3.1.0\] =&gt; 2021-MAY-15

#### Added

* New validator: `DateEquals` which can help you validate that a target value is a date and is the same date as the validation date or other field
* New validator: `After` which can help you validate that a target value is a date and is after the validation date
* New validator: `AfterOrEqual` which can help you validate that a target value is a date and is after or equal the validation date
* New validator: `Before` which can help you validate that a target value is a date and is before the validation date
* New validator: `BeforeOrEqual` which can help you validate that a target value is a date and is before or equal the validation date
* New `onError( closure ), onSuccess( closure )` callbacks that can be used to validate results using the `validate()` method and concatenate the callbacks.
* New `assert()` helper that can assit you in validating truthful expressions or throwing exceptions
* Two new helpers: `validateIsNullorEmpty()` and `validateHasValue()` so you can do simple validations not only on objects and constraints.
* `RequiredIf, RequiredUnless` can now be declared with a simple value pointing to a field. Basically testing if `anotherField` exists, or unless `anotherField` exists.
* New `BaseValidator` for usage by all validators to bring uniformity, global di, and helpers.

#### Changed

* The `IValidator` removes the `getName()` since that comes from the `BaseValidator` now.
* The `UniqueValidator` now supports both creation and update checks with new constraints.
* Removed hard interface requirements to avoid lots of issues across CFML engines. Moved them to the `interfaces` folder so we can continue to document them and use them without direct compilation.

#### Fixed

* Metadata for arguments did not have the right spacing for tosn of validators.
* Added the missing `rules` struct argument to several validators that missed it.

