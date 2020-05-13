# Relay functions

Relay provides some utility functions that help you transform data as it passes through your workflow. They are invoked in a workflow like: `!Fn.<function> [<arguments>]`. See the [Relay Tags reference](relay-types.md) for more information on using custom types.

## append

The `append` function adds one or more values to the end of an array. For example:

```yaml
targets: !Fn.append 
- [a.example.com]
- b.example.com
```

## concat

The `concat` function joins two or more strings together. For example:

```yaml
domains:
- !Fn.concat [!Parameter environment, .example.com]
```

## jsonUnmarshal

The `jsonUnmarshal` function converts serialized JSON into an executable data type. For example:

```yaml
certs: !Fn.jsonUnmarshal [!Secret certs]
```

Because Relay secrets are always stored as strings, the `jsonUnmarshal` function is useful when you need to pass the contents of a secret into a function that requires a data type other than a string.

## merge

The `merge` function iteratively merges together two map arrays. For example:

```yaml
api: !Fn.merge
- image:
    tag: !Secret api.image.tag
- storage:
    address: 10.11.12.13
```

Output:

```yaml
api:
  image:
    tag: !Secret api.image.tag
  storage:
    address: 10.11.12.13
```

## equals

Accompanies the `when` keyword for conditional step execution. The `equals` function compares two arguments and returns the boolean `true` if the type and value of the arguments are equal.

```yaml
when:
  - !Fn.equals [!Parameter env, production]
```

Accepts the following expressions and types:

-   a parameter: `!Parameter example-parameter`
-   an output: `!Output stepName outputName`
-   a string: `"Just a normal string."`
-   a boolean: `true` or `false`
-   an integer: `10`, `100`, `35`
-   a float: `10.5`, `1.0`, `54.99999`
-   an array: `["apple", "banana", "orange", "peach"]`
-   a map: `{"vegetable": "carrot", "fruit": "apple"}`

For more information, see [Conditional step execution](../using-workflows/conditionals.md).

## notEquals

Accompanies the `when` keyword for conditional step execution. The `notEquals` function compares two arguments and returns the boolean `true` if the type and value of the arguments are not equal.

```yaml
when:
  - !Fn.notEquals [!Parameter env, development]
```

Accepts the following expressions and types:

-   a parameter: `!Parameter example-parameter`
-   an output: `!Output stepName outputName`
-   a string: `"Just a normal string."`
-   a boolean: `true` or `false`
-   an integer: `10`, `100`, `35`
-   a float: `10.5`, `1.0`, `54.99999`
-   an array: `["apple", "banana", "orange", "peach"]`
-   a map: `{"vegetable": "carrot", "fruit": "apple"}`

For more information, see [Conditional step execution](../using-workflows/conditionals.md).

