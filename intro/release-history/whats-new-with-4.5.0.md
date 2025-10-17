---
description: February 19, 2025
---

# What's New With 4.5.0

This release adds new features for applying default values to validated objects and expands platform support.

## Removed

* `eurodate` validator has been removed. It doesn't work consistently across ACF/Lucee as they use different standards. Users can now validate dates as they see fit for their specific use case.

## Added

* **BoxLang certification** - cbValidation is now certified to work with BoxLang
* **Lucee 6 support** - Testing and support for Lucee 6.x
* **Adobe 2023 support** - Testing and support for Adobe ColdFusion 2023
* **`defaultValue` constraint** - Apply default values to fields before constraints are checked, useful for initializing optional fields with sensible defaults

See Release Notes on Github: [https://github.com/coldbox-modules/cbvalidation/releases/tag/v4.5.0](https://github.com/coldbox-modules/cbvalidation/releases/tag/v4.5.0)
