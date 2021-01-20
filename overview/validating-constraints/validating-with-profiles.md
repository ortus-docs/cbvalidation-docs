# Validating With Profiles

cbValidation 2.x series introduced the ability to validate using field `profiles`.  This will allow you to define all your constraints but also define field profiles where you can define only certain fields to be validated if the profile name is used.  

## Defining Profiles

This is using the `this.constraintProfiles` struct literal:

```javascript
this.constraintProfiles = {
	"new" = "fname,lname,email,password",
	"update" = "fname,lname,email",
	"passUpdate" = "password,confirmpassword"
}
```

The **key** is the **name** of the profile and the **value** is a list of the fields to validate if the profile is targeted for validation.

## Validating Profiles

Every validation method: `validate(), validateOrFail()` has a `profiles` argument. You can then pass one or more to the argument so you can validate 1 or more profiles:

```javascript
var results = validateModel( target=model, profiles="update" )
var results = validateModel( target=model, profiles="update,passUpdate" )
```

