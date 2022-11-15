---
description: November 15, 2022
---

# What's New With 4.1.0

This is a minor release that includes new validators and also integration with ColdBox 7 delegates.  This will now allow objects to have validatable traits:

```javascript
component name="User" delegates="Validatable@cbValidation"{

}
```

This will give the target object the delegates methods available to it.

### Added

* New ColdBox 7 delegate: `Validatable@cbValidation` which can be used to make objects validatable
* New validators: `notSameAs, notSameAsNoCase`

### Changed

* All date comparison validators now validate as `false` when the comparison target dates values are NOT dates instead of throwing an exception.
