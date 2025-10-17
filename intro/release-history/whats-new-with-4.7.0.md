---
description: October 13, 2025
---

# What's New With 4.7.0

This release enhances the `validateOrFail()` method to provide better filtering of nested structures and arrays, ensuring that only validated fields are returned in the result.

## Changed

* `validateOrFail` now filters nested structs and arrays to only return keys matching constraints, not just top-level keys ([PR #85](https://github.com/coldbox-modules/cbvalidation/pull/85))

See Release Notes on Github: [https://github.com/coldbox-modules/cbvalidation/releases/tag/v4.7.0](https://github.com/coldbox-modules/cbvalidation/releases/tag/v4.7.0)
