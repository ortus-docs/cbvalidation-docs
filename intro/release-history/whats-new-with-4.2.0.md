---
description: April 14, 2023
---

# What's New With 4.2.0

### Added

* New github action versions and consolidation of actions
* New [Contributing](https://github.com/coldbox-modules/cbvalidation/blob/v4.2.0/CONTRIBUTING.md) guidelines
* New github support templates

### Changed

* The way custom validators are retrieved so they are ColdBox 7+ compatible
* `pr` github action now just does format checks to avoid issues with other repos.
* Consolidated Adobe 2021 scripts into the server scripts

### Fixed

* Fix for `tasks.json` file to include no recursion
* [#71](https://github.com/coldbox-modules/cbvalidation/issues/71) - ValidationManager errors when returning `validatedKeys` due to `sharedconstraint` name
* [#45](https://github.com/coldbox-modules/cbvalidation/issues/45) - `Type` validator needs to be able to validate against `any` type even if that is an empty string
