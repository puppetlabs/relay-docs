# Relay functions

Relay provides functions that help you manipulate data as it passes through your workflow. You can invoke a function in a workflow using the following syntax: `!Fn.<function> [<arguments>]`. Most functions are "utility" functions, meaning they can be used anywhere a hard-coded value can appear. There are also a pair of special functions (`equals` and `notEquals`) for evaluating conditionals.

## Utility Functions

### append

The `append` function adds one or more values to the end of an array. For example:

```yaml
targets: !Fn.append
- a.example.com
- b.example.com
```

### coalesce

Returns the first resolvable non-null value from the arguments.

Arguments:
* 0..n: The values to consider, in order

```yaml
!Fn.coalesce [!Output [step, nonexistent], 42] # => 42
!Fn.coalesce [!Output [step, nonexistent]] # => null (used to ignore non-resolvable values)
!Fn.coalesce [!Output [step, nonexistent], !Parameter someParameterWithNullValue, 42] # => 42
```

### concat

The `concat` function joins two or more elements together. For example:

```yaml
domains:
- !Fn.concat [foo, 3, bar, ".com"] # => "foo3bar.com"
- !Fn.concat [!Parameter environment, .example.com]
```

### convertMarkdown

The `convertMarkdown` function converts [Markdown](https://daringfireball.net/projects/markdown/) into comparable syntax for similar specifications. For example:

```yaml
description: !Fn.convertMarkdown [jira, !Parameter markdown]
```

Currently supported specifications:

* [`jira`](https://jira.atlassian.com/secure/WikiRendererHelpAction.jspa?section=all)
* [`slack`](https://api.slack.com/reference/surfaces/formatting)
* `html`

### jsonUnmarshal

The `jsonUnmarshal` function converts serialized JSON into an executable data type. For example:

```yaml
certs: !Fn.jsonUnmarshal [!Secret certs]
```

Because Relay secrets are always stored as strings, the `jsonUnmarshal` function is useful when you need to pass the contents of a secret into a function that requires a data type other than a string.

### merge

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

### path

Looks up a path using a query in an object, optionally returning a default value if the path is unresolvable or nonexistent.

Arguments:
1. The object to traverse
2. The query to use
3. optional: The default to use instead if the object does not contain a value at the query or cannot be resolved

```yaml
!Fn.path [{foo: bar}, foo] # => "bar"
!Fn.path [{foo: bar}, baz] # => (invocation error)
!Fn.path [{foo: bar}, baz, quux] # => "quux"
!Fn.path [{foo: {bar: [{baz: quux}]}}, "foo.bar[0].baz"] # => "quux"
!Fn.path [{foo: !Output [step, nonexistent]}, foo] # => (unresolvable)
!Fn.path [{foo: !Output [step, nonexistent]}, foo, 42] # => 42
```

### toString
Converts its argument to a string if possible, and returns an error otherwise.

Argument:
1. The scalar value to convert to a string

```yaml
!Fn.toString [foo] # => "foo"
!Fn.toString [42] # => "42"
!Fn.toString [42.424242] # => "42.424242"
```

## Conditional Functions

These functions are only useful in the context of a `when:` statement for conditionally executing a step.

For more information, see [Conditional step execution](../using-workflows/conditionals.md).

### equals

The `equals` function compares two arguments and returns the boolean `true` if the type and value of the arguments are equal.

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

### notEquals

The `notEquals` function compares two arguments and returns the boolean `true` if the type and value of the arguments are not equal.

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
