# Domain Object

Within any domain object you can define a public variable called `this.constraints` that is a assigned an implicit structure of validation rules for any fields or properties in your object.

### Declaration

{% code title="models/User.cfc" %}
```javascript
component persistent="true"{

    // Object properties
    property name="id" fieldtype="id" generator="native" setter="false";
    property name="fname";
    property name="lname";
    property name="email";
    property name="username";
    property name="password";
    property name="age";

    // Validation
    this.constraints = {
        // Constraints go here
    }
}
```
{% endcode %}

We can then create the validation rules for the properties it will apply to it:

{% code title="config/User.cfc" %}
```javascript
component persistent="true"{

    ...

    // Validation
    this.constraints = {
        fname = { required = true },
        lname = { required = true},
        username = {required=true, size="6..10"},
        password = {required=true, size="6..8"},
        email = {required=true, type="email"},
        age = {required=true, type="numeric", min=18}
    };
}
```
{% endcode %}

That easy! You can just declare these validation rules and ColdBox will validate your properties according to the rules. In this case you can see that a password must be between 6 and 10 characters long, and it cannot be blank.

{% hint style="info" %}
By default all properties are of type **string** and **not** required
{% endhint %}

### Usage

You can then use them implicitly when calling our validation methods:

```javascript
validate( myUser );
validateOrFail( myUser );
```
