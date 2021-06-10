# Relay template functions

Relay provides functions that help you manipulate your data. You can call these functions inside a [template expression](relay-expressions.md) anywhere templating is permitted in a workflow.

For example, you could use the `append` and `jsonUnmarshal` functions to create an array for your step to consume like this:

```yaml
steps:
- name: process-safe-ips
  image: alpine
  spec:
    ips: ${append(jsonUnmarshal(secrets.ips), '127.0.0.1', '192.168.0.1')}
```

## append

> Positional syntax: `append(array, value[, value[, ...]])`

The `append` function adds one or more values to the end of an array.

Example:

```
append(['foo', 'bar'], 'baz', 'quux') # => ["foo", "bar", "baz", "quux"]
```

## coalesce

> Positional syntax: `coalesce([value[, value[, ...]]])`

Returns the first resolvable non-null value from the arguments.

Examples:

```
coalesce(outputs.step.nonexistent, 42) # => 42
coalesce(outputs.step.nonexistent) # => null
coalesce(outputs.step.nonexistent, parameters.nullValue, 42) # => 42
```

## convertMarkdown

> Positional syntax: `convertMarkdown(to, content)`

> Keyword syntax: `convertMarkdown(to: <string>, content: <string>)`


The `convertMarkdown` function converts [Markdown](https://daringfireball.net/projects/markdown/) into comparable syntax of other languages.

Supported languages for the `to` argument:

* [`jira`](https://jira.atlassian.com/secure/WikiRendererHelpAction.jspa?section=all)
* [`slack`](https://api.slack.com/reference/surfaces/formatting)
* `html`

Examples:

```
convertMarkdown('jira', parameters.markdown)
convertMarkdown(to: 'jira', content: parameters.markdown)
```

## jsonMarshal

> Positional syntax: `jsonMarshal(value)`

The `jsonMarshal` function encodes the given value as JSON.

Example:

```
jsonMarshal({'foo': ['bar']}) # => "{\"foo\":[\"bar\"]}"
```

## jsonUnmarshal

> Positional syntax: `jsonUnmarshal(string)`

The `jsonUnmarshal` function decodes serialized JSON from a string into data the template engine can work with.

Because Relay secrets are always stored as strings, the `jsonUnmarshal` function is useful when you need to pass the contents of a secret into a function that requires a data type other than a string.

Example:

```
jsonUnmarshal(secrets.certs)
```

## merge

> Positional syntax: `merge([map[, map[, ...]]])`

> Keyword syntax: `merge(objects: <array of maps>, mode: 'deep'|'shallow')`

The `merge` function iteratively combines maps. If the mode is deep (the default), the maps will be recursively merged such that any keys that are themselves maps are also merged. Otherwise, only the top-level values are merged, and any collisions result in the data being completely replaced.

Examples:

```
merge({'a': 'b'}, {'c': 'd'}) # => {"a": "b", "c": "d"}
merge(objects: [{'a': 'b'}, {'c': 'd'}]) # => {"a": "b", "c": "d"}
merge({'a': 'b'}, {'a', 'd'}) # => {"a": "d"}
merge({'a': {'b': 'c'}}, {'a': {'d': 'e'}}) # => {"a": {"b": "c", "d": "e"}}
merge(
  objects: [{'a': {'b': 'c'}}, {'a': {'d': 'e'}}],
  mode: 'shallow'
) # => {"a": {"d": "e"}}
```

## now

> Positional syntax: `now()`

The `now` function returns the current time at UTC in ISO 8601 format.

Example:

```
now() # => "2021-06-09T00:12:54Z"
```

## path

> Positional syntax: `path(object, query[, default])`

> Keyword syntax: `path(object: <value>, query: <string>, default: <value>)`

The `path` function queries an object using the template expression syntax and optionally returns a default value if the requested path does not exist. Note that functions are not available to use in the `path` function's query argument.

Examples:

```
path({'foo': 'bar'}, 'foo') # => "bar"
path({'foo': 'bar'}, 'baz') # Error
path({'foo': 'bar'}, 'baz', 'quux') # => "quux"
path({'foo': {'bar': [{'baz': 'quux'}]}}, 'foo.bar[0].baz') # => "quux"
path(object: {'foo': 'bar'}, query: 'foo', default: 'quux') # => "bar"
```

## toString

> Positional syntax: `toString(value)`

The `toString` function returns a string representation of its scalar argument. It is an error to pass a map or array to `toString`; consider `jsonMarshal` instead.

Examples:

```
toString('foo') # => "foo"
toString(42) # => "42"
toString(42.424242) # => "42.424242"
```
