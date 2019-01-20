# Declaring Constraints

You can define constraints in several locations:

1. [Configuration file](configuration-file.md)
2. [Inside domain object](domain-object.md)
3. [A-la-carte via event handlers](a-la-carte-via-event-handlers.md)

When validating using the mixin `validateModel()` or `getValidationManager.validate()` you have to specify a target, but specifying a constraint in your call is optional.

* If you don't specify a constraint parameter, `validateModel(myTarget)` will lookup your constraints in `myTarget.constraints` struct in your target object or struct.
* If you specify your constraint parameter as a string, the validator will lookup a shared constraint in your configuration file.
* If you specify your constraint parameter as a struct, this struct will directly server as your set of constraints, so you can specify your constraints on the fly,  or specify an alternative set of constraints in your model, e.g `User.constraints` vs `User.signInConstraints`

