# Developing steps with the Relay CLI

Relay has an internal API called the metadata service, which presents a REST endpoint to step containers. It serves up dynamic information from workflow steps' `spec` sections and receives outputs from steps as well as events from trigger containers.

In order to shorten the development loop for writing, testing, and debugging step code, the `relay` CLI tool has a built-in version of the metadata service that you can run locally. This will allow your step entrypoint code to run unmodified from the shell, rather than requiring a container build, push, and execute cycle up to the Relay service.

The metadata service is available under the `relay dev metadata` subcommand. It reads in a YAML file containing mock data that you supply, then starts an HTTP server. The server responds to the API endpoints that the Relay client SDKs expect to query on the service, supplying your mock values. This enables you to rapidly test and debug how your code will work when presented with user input on the service.

Because Relay steps don't normally exist in isolation, the YAML input is set up to mock an entire workflow's configuration and includes provisions for [secrets](https://relay.sh/docs/using-workflows/managing-secrets/), [connections](https://relay.sh/docs/using-workflows/managing-connections/), and [outputs](https://relay.sh/docs/using-workflows/passing-data-into-workflow-steps). As such, the lexical structure of the YAML looks like this:

```yaml
# filename: test-metadata.yaml
# map of connections, named '<type>/<name>'
connections:
  aws/my-aws-connection:
    accessKeyID: AKIASAMPLEKEY
   secretAccessKey: ...

# single string valued secrets
secrets:
  mySecretKey: s00pers33kr1t

# now the data to be fed into pseudo-run 1 of this workflow
runs:
  '1':      # quoted string, not numeric! thanks YAML
    steps:
      first-step:
        # the `spec` keys match what you'd see in a real workflow
        spec:
          aws: !Connection [aws, test]
          message: My test message
          secret: !Secret mySecretKey
        # outputs can be strings or structured data
        outputs:
          instanceIDs:
          - 125252025
          - 252598675
          outputMessage: "This came from the step!"
```

Additional steps may then reference `!Output [first-step, instanceIDs]` in their `spec` section to retrieve the array of IDs.

After constructing the YAML input, start up the metadata service with:

```
relay dev metadata --input test-metadata.yaml --run 1 --step first-step
```

The last line of output contains an environment variable that you'll need to copy-and-paste into the shell where you're debugging you entrypoint:

```
No command was supplied, awaiting requests. Set environment with:
export METADATA_API_URL='http://:VeRyLoNgJWT[::]:59025'
```

This is the environment variable that the Python and Go SDKs as well as the `ni` shell tool use to communicate with the metadata service in Relay; setting it locally will point your code at the mock service. Running your code should result in HTTP GETs against the `/spec` path for each parameter and HTTP PUTs against the `/output` path if the step sets output variables.

Alternately, you can append the command line you'd run to test your code to the invocation of `relay dev metadata` and the environment variable will be set for you. This might be preferable if you are making changes to the YAML input file instead of, or concurrently with, changing the entrypoint code.

```
relay dev metadata --input test-metadata.yaml --run 1 --step first-step python3.8 ./my-entrypoint.py
```