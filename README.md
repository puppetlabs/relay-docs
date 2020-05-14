# Documentation for relay.sh

This repo contains the source files which get rendered into the user-facing documentation for [Relay by Puppet](https://relay.sh). Contributions are welcome!

## Relay style

A few points of style for writers:

* When we refer to the product, use a capital-R Relay; when referring to the command-line tool use the backtick code `relay`. Only the initial mention at top-level docs like the Overview (or this README) needs the "Relay by Puppet" moniker.
* Use short, action-oriented sentences that position Relay as the "actor". For example, "Relay stores secrets for you" is better than "Your secrets are stored in Relay" because it both uses an action verb and refers to the service as a distinct entity. (Apple's style for in-product copy on iPhone and Mac is the precedent here: "iPhone needs your password to enable TouchID after a restart")
* Workflow examples should be valid and complete (if trivial). Rather than single-line snippets, use inline code with backticks until there's enough context to provide a running workflow.
* Provide alt text with a meaningful textual description of each image for accessibility. "Screenshot of run UI" is bad alt text; "On the workflow page, there is a list of each completed run" is much better.

