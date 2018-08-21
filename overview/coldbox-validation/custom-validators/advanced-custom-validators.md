# Advanced Custom Validators

You can use multiple custom validators or pass in arbitrary data to custom validators by specifying the validator name as the key in the rules struct.

```javascript
this.constraints = {
    existsInDBValidator = {
        table = "table_name",
        column = "column_name"
    }
};
```

This example will look for an `existsInDBValidator` in WireBox and pass in `{ table = "table_name", column = "column_name" }` to the `validate` method.

Using these advanced techniques you can build reusable validators that accept the data they need in the `validationData` struct. You can also include multiple custom validators just by specifying each of them as a key.

If you don't have any custom data to pass to a validator, just pass an empty struct \(`{}`\)

