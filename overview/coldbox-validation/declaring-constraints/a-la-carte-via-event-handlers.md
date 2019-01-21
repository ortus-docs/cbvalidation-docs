# A-la-carte via event handlers

You can define your constraints in your validation target or in a sharedConstraint but sometimes you need something more flexible. In this case you can define your constraints on the fly in your handler.

In this sample we validate the public request context `rc`. This sample validates all fields in the `rc`.  If you need more control you can specify the `fields` parameter \(default all\) or the `includeFields` and `excludeFields` parameters in your `Validatemodel()` call.

```javascript
// sample REST API create user
function create( event, rc, prc ){
	var validationResult = validateModel( 
		target = rc,
		constraints = { 
			username = { required = true },
			email = { required = true, type = "email" },
			password = { required = true }
		}
	)
	if ( !validationResult.hasErrors() ) {
		UserService.createUser(rc.username, rc.email, rc.password);
		prc.response.setData( UserService.readUser(username=rc.username) );
	} else {
		prc.response
			.setError( true )
			.addMessage( validationResult.getAllErrors())
			.setStatusCode( STATUS.BAD_REQUEST )
			.setStatusText( "Validation error" );
	}
}
```

