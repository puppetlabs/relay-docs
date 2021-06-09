# Relay expression language

While it's possible to write Relay workflows entirely using constant data, they're generally much more useful when they can handle dynamic inputs and outputs.

In certain parts of your workflow, you can use Relay's template language to access data dynamically. This includes:

- [The `spec` key of a step](../using-workflows/passing-data-into-a-workflow-step.md)
- [The `env` key of a step](relay-workflows.md#env)
- [The `when` keys of steps and triggers](../using-workflows/conditionals.md)
- [The `binding` key of a trigger](../using-workflows/using-triggers.md)

Template expressions are delimited by `${` and `}`. For example, you may write the following in a step's spec:

```yaml
spec:
  calculation: ${2 + 2}
```

When the step requests the `calculation` key from the spec, it will receive the value `4`.

You can also use template expressions inside other strings. When you do this, the value produced by an expression will be automatically coerced to a string. Non-scalar types like maps and arrays will be converted to JSON. Here's a simple example:

```yaml
spec:
  message: Looks like it's ${now()}. Time for a nap!
```

When the step requests the `message` key, it will receive a string value like "Looks like it's 2021-06-08T21:38:34Z. Time for a nap!"

## Types

Template expressions are generally designed to work in tandem YAML/JSON, and they support most of the same data types:

### Strings

String literals are written using either single or double quotes. Use a backslash (`\`) to introduce an escape character.

### Numbers

Integers and floats are supported. When performing arithmetic or other operations that return numbers, a float value is always returned instead of an integer. This is similar to how JSON handles numbers.

Because we use floats extensively, they are subject to both precision issues for extremely large and extremely small values and inaccuracy due to rounding errors. If your workflow requires precise handling of numeric values (for example, working with money), you should encode your numeric values as strings and perform any required operations in your step code.

### Booleans

Use the identifiers `true` and `false` to represent boolean values.

### Null

Use the identifier `null` to represent the null value.

### Maps (objects)

Maps are key-value pairs. Keys always have a string type, while values may be of any supported type. Map entries are not ordered.

You can construct a map literal using `{` and `}`, like a JSON object:

```yaml
spec:
  map: ${{'foo': 'bar'}}
```

### Arrays

Arrays are ordered collections of values.

You can construct a map literal using `[` and `]`, like in JSON:

```yaml
spec:
  map: ${['foo', 1, 2, true]}
```

## Operations

### Indexing

You can index into maps and arrays using either of two ways, a property syntax or a bracket syntax.

The property syntax uses dots to separate access. The bracket syntax puts the index to access inside square brackets, `[` and `]`. For example, given the following structure:

```json
{
  "foo": {
    "bar": "baz",
    "quux": [true, 2, "hello"]
  },
  "hello-to": "world"
}
```

Here are some possible ways to access data from it, and the resulting values:

- `foo.bar # => "baz"`
- `foo['bar'] # => "baz"`
- `foo['hello-to'] # => "world"`
- `foo.'hello-to' # => "world"`
- `foo.quux[0] # => true`
- `foo.quux.0 # => true`
- `foo['quux'][1] # => 2`
- `foo['quux'][1 + 1] # => "hello"`
- `foo.quux.(1 + 1) # => "hello"`

We don't provide an explicit recommendation on which indexing operators you should use. Use whatever is the most readable in your workflow. If you're constructing expressions programmatically, it's often easiest to use the bracket syntax as there is no possibility of ambiguity.

### Boolean

The following operators are supported:

- Not: `!`, unary
- Or: `||`, binary
- And: `&&`, binary

Operands will be coerced to a boolean before being applied. As a special case, the strings `"true"` and `"TRUE"` are considered to be equal to `true` and the strings `"false"` and `"FALSE"` are considered to be equal to `false`. Null is considered falsy.

All binary operators support short-circuiting.

### Comparison

The following operators are supported for all types:

- Equal: `==`, binary
- Not equal: `!=`, binary

The following operators are supported for strings (lexicographically) and numbers:

- Greater than: `>`, binary
- Greater than or equal: `>=`, binary
- Less than: `<`, binary
- Less than or equal: `<=`, binary

### Concatenation: `+`, binary

Two strings can be combined using the `+` operator. If either of the operands is not a string, it will be coerced to a string first using [Go's `%v` formatter](https://golang.org/pkg/fmt/#hdr-Printing).

### Arithmetic

The following operators are supported for numbers:

- Addition: `+`, binary
- Subtraction: `-`, binary
- Multiplication: `*`, binary
- Division: `/`, binary
- Modulus: `%`, binary (floating-point remainder, sign of dividend)
- Exponentiation: `**`, binary

### Pipeline: `|>`, binary

The binary pipeline operator allows you to chain expressions. It sets the input data for the following expression to the result of the preceding expression. For example:

```yaml
spec:
  combined: ${jsonUnmarshal('{"a": 1, "b": 2}') |> a + b}
```

When the step requests the value of `combined` from the spec, it will get the value `3`.

### Parentheses

Parentheses allow you to evaluate an expression within another expression with the highest precedence. For example:

```yaml
spec:
  calculation: ${(1 + 2) * 3}
```

## Functions

Relay provides a small library of utility functions to make working with complex data a bit easier. When you've exhausted the possibilities of Relay's function library, you can often find what you need in a step, like our [jq](https://relay.sh/steps/stdlib/jq) step.

The expression language supports two ways to call functions, positional or keyword argument invocation. Unlike other languages like Python, the argument patterns cannot be mixed in a single invocation.

For example, the `merge` function can be called as follows:

- `merge({'a': 'b'}, {'c': 'd'}) # => {"a": "b", "c": "d"}`
- `merge(objects: [{'a': 'b'}, {'c': 'd'}], mode: 'shallow') # => {"a": "b", "c": "d"}`

See the [Relay function reference](relay-functions.md) for the full list of functions and their usage.

## Input data

When templates are evaluated, they are given input data by the workflow engine. This data allows you to make decisions about what the workflow should do.

The current top-level input data is always available as the value `$`. In fact, when you're really stuck, it can sometimes be useful to simply print the entire set of data available to you:

```yaml
steps:
- name: everything
  image: alpine
  spec:
    data: ${$}
  input:
  - ni get
```

If you're indexing into top-level data, you can omit the initial `$`. That is, `$.foo` and `foo` are equivalent ways to access the `foo` key from the input data map.

**WARNING:** If your account has connections or your workflow has secrets, this step will print all of that data to the log. Be careful to put this into a test workflow that you delete later or choose a subset of the data to print instead.

Relay always provides a map as the initial input data. Depending on what part of the workflow you're in, Relay makes some of the following keys available in the map:

### `connections`

> Available in: Step and trigger specs, step `env`, step `when` conditions

You can reference a [connection](../using-workflows/managing-connections.md) in your account using its type and name. Steps that require a connection to run look like this:

```yaml
steps:
- name: describe-instances
  image: relaysh/ec2-step-describe-instances
  spec:
    aws:
      connection: ${connections.aws.'my-aws-account'}
      region: us-west-2
```

### `secrets`

> Available in: Step and trigger specs, step `env`, step `when` conditions

You can reference a [secret](../using-workflows/managing-secrets.md) stored in your workflow. For example:

```yaml
steps:
- name: use-a-secret
  image: alpine
  spec:
    pass: ${secrets.password}
```

### `parameters`

> Available in: Step specs, step `env`, step `when` conditions

You can reference a [parameter](../using-workflows/passing-data-into-workflow-steps.md) provided to the current workflow run (manually or by a trigger binding). For example:

```yaml
parameters:
  myParameter:
    description: A parameter the user needs to provide
    default: "A silly default value"

steps:
- name: use-a-parameter
  image: alpine
  spec:
    userParam: ${parameters.myParameter}
```

### `outputs`

> Available in: Step specs, step `env`, step `when` conditions

You can reference [outputs](../using-workflows/passing-data-into-workflow-steps.md) from previous steps. For example:

```yaml
steps:
- name: make-output
  image: alpine
  input:
  - ni output set --key random --value "$(dd if=/dev/urandom bs=1 | head -c30 | base64)"

- name: use-output
  image: alpine
  spec:
    stuff: ${outputs.'make-output'.random}
```

Generally, when you reference an output, Relay can automatically determine that it needs to create a dependency relationship with the step that is being referenced. For example, when you write `${outputs.foo.bar}`, Relay will automatically insert `foo` into the `dependsOn` for the step.

However, it is possible to write sufficiently complex expressions using outputs that this cannot be determined automatically. If you dynamically choose a step to reference, for example, you'll need to enumerate all of the possible dependencies in the step's `dependsOn` key by hand. You should always check the execution graph that Relay generates to be sure it meets your expectations.

### `event`

> Available in: Trigger `when` conditions, trigger bindings

You can reference data from an [event](../using-workflows/using-triggers.md) sent to a Relay trigger. For example:

```yaml
triggers:
- name: github
  source:
    type: webhook
    image: relaysh/datadog-trigger-event-fired
  binding:
    parameters:
      what: ${event.title}

parameters:
  what:
    description: The thing to alert on
```
