# Valid Constraints

## Supported constraints

Below are all the currently supported constraints. If you need more you can create your own [Custom validators](../custom-validators/).

```javascript
propertyName = {
	// required field or not, includes null values
	required : boolean [false],
	
	// specific type constraint, one in the list.
	type  : (ssn,email,url,alpha,boolean,date,usdate,eurodate,numeric,GUID,UUID,integer,string,telephone,zipcode,ipaddress,creditcard,binary,component,query,struct,json,xml),

	// size or length of the value which can be a (struct,string,array,query)
	size  : numeric or range, eg: 10 or 6..8
	
	// range is a range of values the property value should exist in
	range : eg: 1..10 or 5..-5
	
	// regex validation
	regex : valid no case regex
	
	// same as another property
	sameAs : propertyName
	
	// same as but with no case
	sameAsNoCase : propertyName
	
	// value in list
	inList : list

	// value is unique in the database via the cborm module, it must be installed
	unique : true
	
	// discrete math modifiers
	discrete : (gt,gte,lt,lte,eq,neq):value
	
	// UDF to use for validation, must return boolean accept the incoming value and target object, validate(value,target):boolean
	udf = variables.UDF or this.UDF or a closure.
	
	// Validation method to use in the target object must return boolean accept the incoming value and target object 
	method : methodName
	
	// Custom validator, must implement coldbox.system.validation.validators.IValidator
	validator : path or wirebox id, example: 'mypath.MyValidator' or 'id:MyValidator'
	
	// min value
	min : value
	
	// max value
	max : value
}
```

## Reference

| Constraint | Type | Default |  |
| :--- | :--- | :--- | :--- |
| required | boolean | false | Whether the property must have a non-null value |
| type | string | string |  Validates that the value is of a certain format type. Our included types are: ssn,email,url,alpha,boolean,date,usdate,eurodate,numeric,GUID,UUID,integer,string,telephone,zipcode,ipaddress,creditcard,binary,component,query,struct,json,xml |
| size |  numeric or range | --- | The size or length of the value which can be a struct, string, array, or query. The value can be a single numeric value or our cool ranges. Ex: size=4, size=6..8, size=-5..0 |
| range | range | --- | Range is a range of values the property value should exist in. Ex: range=1..10, range=6..8 |
| regex | regular expression | --- | The regular expression to try and match the value with for validation. This is a no case regex check. |
| sameAs | propertyName | --- | Makes sure the value of the constraint is the same as the value of another property in the object. This is a case sensitive check. |
| sameAsNoCase | propertyName | --- | Makes sure the value of the constraint is the same as the value of another property in the object with no case sensitivity. |
| inList | string list | --- | A list of values that the property value must exist in |
| discrete | string | --- | Do discrete math in the property value. The valid values are: eq,neq,lt,lte,gt,gte. Example: discrete="eq:4" or discrete="lte:10" |
| udf | UDF or closure | --- | I can do my own custom validation by doing an inline closure \(CF 10 or Railo only\) or a pointer to a custom defined function. The function must return boolean and accepts two parameters: value and target. |
| method | method name | --- | The name of a method to call in the target object for validation. The function must return boolean and accepts two parameters: value and target. |
| min | numeric | --- | The value must be greater than or equal to this minimum value |
| max | numeric | --- | The value must be less than or equal to this maximum value |
| validator | instantiation path or wirebox DSL | --- | You can also build your own validators instead of our internal ones. This value will be the instantiation path to the validator or a wirebox id string. Example: validator="mymodel.validators.MyValidator", validator="id:MyValidator" |

With the `validator` constraint you can specify your own custom validator, but if you need your own parameters for your validator this is a bit limited. You can also specify `YourOwnValidator` as constraint label where `YourOwnValidator` is a wirebox id string. In this  case you can specify your own parameters. See [Advanced Custom Validators](../custom-validators/advanced-custom-validators.md) for details.

{% hint style="warning" %}
WARNING: You can't do a normal wirebox mapping for `YourOwnValidator` in your main application. A validator needs an `IValidator` interface from the `cbvalidation` module. When wirebox inspects the binder, the `cbvalidation` module is not loaded yet, so it will error. This can be solved by defining your custom validators in an own module \(depending on `cbvalidation`\) or by mapping your validator in the `afterConfigurationLoad()` method of your binder, e.g in `config/wirebox.cfc`
{% endhint %}



