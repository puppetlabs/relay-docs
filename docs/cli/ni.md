## ni

Nebula Interface

### Synopsis

The ni tool is meant to be run inside a Relay (FKA "Nebula")
step container to provide helpful SDK-like utilities from the shell.
Invoke it from your relay steps to access parameters and secrets,
generate logs that will be stored on the service, and pass data
between steps.


### Subcommand Usage

**`ni aws config`** -- Create an AWS configuration suitable for using with an AWS CLI or SDK
```
  -d, --directory string   configuration output directory
```

**`ni aws env`** -- Create a POSIX-compatible script that can be sourced to configure the AWS CLI
```
  -d, --directory string   configuration output directory
```

**`ni cluster config`** -- Create cluster config
```
  -d, --directory string   configuration output directory
```

**`ni credentials config`** -- Create credentials configuration
```
  -d, --directory string   configuration output directory
```

**`ni doc [flags]`** -- build command documentation
```
  -f, --file string   The path to a file to write the documentation to
```

**`ni file`** -- WriteFile specification data to a file
```
  -f, --file string     file name
  -o, --output string   output type
  -p, --path string     specification data path
```

**`ni gcp config`** -- Create a GCP configuration suitable for using with a GCP CLI or SDK
```
  -d, --directory string   configuration output directory
```

**`ni gcp env`** -- Create a POSIX-compatible script that can be sourced to configure the GCP CLI
```
  -d, --directory string   configuration output directory
```

**`ni get`** -- Get specification data
```
  -p, --path string   specification data path
```

**`ni git clone`** -- Clone git repository
```
  -d, --directory string   git clone output directory
  -r, --revision string    git revision
```

**`ni log error`** -- Logs an error message

**`ni log fatal`** -- Logs a fatal error message and exits process

**`ni log info`** -- Logs an informational message

**`ni log warn`** -- Logs a warning message

**`ni output set`** -- Set a value to a key that can be fetched by another task
```
      --json           whether the value should be interpreted as a JSON string
  -k, --key string     the output key
  -v, --value string   the output value
```

