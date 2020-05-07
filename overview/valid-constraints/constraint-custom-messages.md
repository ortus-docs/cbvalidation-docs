# Constraint Custom Messages

By default if a constraint fails an error message will be set in the result objects for you in English. If you would like to have your own custom messages for specific constraints you can do so by following the constraint message convention:

```javascript
{constraintName}Message = "My Custom Message";
```

Just add the name of the constraint you like and append to it the word Message and you are ready to roll:

```javascript
username = { 
    required="true", 
    requiredMessage="Please enter the username", 
    size="6-8", 
    sizeMessage="The username must be between 6 to 8 characters" 
}
```

