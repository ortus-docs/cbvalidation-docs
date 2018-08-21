# Custom Validation Managers

If you would like to adapt your own validation engines to work with ANY ColdBox application you can do this by implementing the following interfaces:

* Validation Manager : Implement the `cbvalidation.models.IValidationManager`. Then use the class path in your configuration file so it uses your validation manager instead of ours.
* Validation Results : Implement the `cbvalidation.models.result.IValidationResult`, which makes it possible for any ColdBox application to use your validation results.
* Validation Error : Implement the `cbvalidation.models.result.IValidationError`, which makes it possible for any ColdBox application to use your validation error representations.

