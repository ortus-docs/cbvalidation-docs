# Validating with a-la-carte constraints

 We also have the ability to validate a target object with custom a-la-carte constraints by passing the constraints inline as an struct of structs. This way you can store these constraint rules anywhere you like.

```javascript
var myConstraints = {
	login = { required=true, size=6..10 }, 
	password = { required=true, size=6..10 }
};
prc.results = validateModel( target=user, constraints=myConstraints );
```

 This will validate the object using the inline constraints that you built.

