# Setting up your editor for Relay

Relay workflows are written in YAML that conforms to a particular specification. It can speed up the development cycle and reduce errors if your authoring environment knows about the specification, because you can get real-time validation and data type checking as you write. Here's how to set that up:

## Visual Studio Code

VS Code is the primary authoring environment Relay supports. In order to get syntax validation for Relay workflows, you'll need to follow these steps:

* Install and enable the [Red Hat maintained YAML plugin](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml)
* Configure the plugin to associate particular YAML files with the Relay schema, and add our custom tag support. The following snippet, which goes in your `settings.json`, matches every YAML that's in a subdirectory named 'relay' with the schema (you may have to modify this for your own environment, see the [plugin's docs for details](https://github.com/redhat-developer/vscode-yaml#associating-a-schema-to-a-glob-pattern-via-yamlschemas))

```json
{
   "yaml.schemas": {
        "https://raw.githubusercontent.com/puppetlabs/relay-core/master/pkg/workflow/asset/data/schemas/v1/Workflow.json": ["relay*/*.yaml"]
    },
        "yaml.customTags": [
            "!Secret scalar",
            "!Parameter scalar",
            "!Output sequence",
            "!Answer sequence",
            "!Data scalar",
            "!Connection map",
            "!Fn.append sequence",
            "!Fn.concat sequence",
            "!Fn.equals sequence",
            "!Fn.jsonUnmarshal sequence",
            "!Fn.notEquals sequence",
            "!Fn.merge sequence",
        ],
    "yaml.validate": true
}
