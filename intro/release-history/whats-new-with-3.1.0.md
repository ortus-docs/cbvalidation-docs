# What's New With 3.1.0

#### Added

* `RequiredIf, RequiredUnless` can now be declared with a simple value pointing to a field. Basically testing if `anotherField` exists, or unless `anotherField` exists.
* New `BaseValidator` for usage by all validators to bring uniformity and helpers.

#### Changed

* The `UniqueValidator` now supports both creationg and update checks with new constraints.
* Removed hard interface requirements to avoid lots of issues across CFML engines. Moved them to the `interfaces` folder so we can continue to document them and use them without direct compilation.

