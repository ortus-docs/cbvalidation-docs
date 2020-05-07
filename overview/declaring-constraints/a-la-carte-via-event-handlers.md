# A-la-carte

You can also define constraints a-la-carte. Meaning you can create them on the fly or store them as JSON or somewhere in a service.  As long as it is a struct of constraints, that's all the validation methods accept via the `constraints` argument.

In this sample we validate the public request context `rc`. This sample validates all fields in the `rc`.  If you need more control you can specify the `fields` parameter \(default all\) or the `includeFields` and `excludeFields` parameters in your `validate()` call.

```javascript
// sample REST API create user
	function create( event, rc, prc ){
		var validationResult = validate(
			target      = rc,
			constraints = {
				username : { required : true },
				email    : { required : true, type : "email" },
				password : { required : true }
			}
		)
		if ( !validationResult.hasErrors() ) {
			UserService.createUser( rc.username, rc.email, rc.password );
			prc.response.setData( UserService.readUser( username = rc.username ) );
		} else {
			prc.response
				.setError( true )
				.addMessage( validationResult.getAllErrors() )
				.setStatusCode( STATUS.BAD_REQUEST )
				.setStatusText( "Validation error" );
		}
	}
```

