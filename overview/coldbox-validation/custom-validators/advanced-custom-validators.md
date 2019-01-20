# Advanced Custom Validators

You can use multiple custom validators or pass in arbitrary data to custom validators by specifying the validator name as the key in the rules struct.

```javascript
//sample custom validator constraints
this.constraints = {
    UniqueInDB = { 
    table= "table_name", 
    column = 'column_name' } },
};
```

This example will look for a `UniqueInDBValidator` in WireBox and pass in `{ table = "table_name", column = "column_name" }` to the `validate` method.

```javascript
//sample validator
/**
* UniqueInDB validator. This checks and returns fals if value is already present in DB
*/
component singleton implements="cbvalidation.models.validators.IValidator" accessors="true"  {
	/**
	 * Constructor
	 */
	UniqueInDB function init(){
		variables.Name = "UniqueInDBValidator";
		return this;
	}
	
	/**
	* validate
	*/
	boolean function validate(
		required cbvalidation.models.result.IValidationResult validationResult,
		required any target,
		required string field,
		any targetValue,
		any validationData
	){
		//check validationdata
		if ( !IsStruct(validationData ) ) {
			throw(message="The validator data is invalid: #arguments.validationData#, it must be a struct with keys 'table' = 'tableName' and 'column' = 'columnName'");
		}
		//check validationdata
		if ( !structKeyExists(validationData,"table") || !structKeyExists(validationData,"column") ) {
			throw(message="The validator data is invalid: #serializeJSON(validationData)# it must be a struct with keys 'table' = 'tableName' and 'column' = 'columnName'");
		}

		var myParams = { table =validationData.table, column=validationData.column, columnvalue=targetValue };
		var sql = "Select #validationData.column# from #validationData.table# where #validationData.column# = :columnvalue";
		var myQuery = queryExecute(sql, myParams);
		if  (myQuery.recordcount == 0) { 
			return true 
		} 
		// error messages definieren
		var args = {
			message="The value #targetValue# is not unique in your database",
			field=arguments.field,
			validationType=getName(),
			validationData=arguments.validationData
		};
		var error = validationResult.newError(argumentCollection=args).setErrorMetadata({table=validationData.table, column=validationData.column});
		validationResult.addError( error );
		return false;
	}

	/**
	* getName
	*/
	string function getName(){
		return variables.Name
	}
}
```

Using these advanced techniques you can build reusable validators that accept the data they need in the `validationData` struct. You can also include multiple custom validators just by specifying each of them as a key.

If you don't have any custom data to pass to a validator, just pass an empty struct \(`{}`\)

{% hint style="warning" %}
WARNING: You can't do a normal wirebox mapping for `YourOwnValidator` in your main application. A validator needs an `IValidator` interface from the `cbvalidation` module. When wirebox inspects the binder, the `cbvalidation` module is not loaded yet, so it will error. This can be solved by defining your custom validators in an own module \(depending on `cbvalidation`\) or by mapping your validator in the `afterConfigurationLoad()` method of your binder, e.g in `config/wirebox.cfc`
{% endhint %}

