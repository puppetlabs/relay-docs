# Documentation

## How to contribute

- We welcome any improvements to the docs. Good documentation is so fundamental to learning and using the product, every little thing counts!
- If you're feeling motivated, and run across something that needs a-fixin', please send in a PR for it!
- Issues are very helpful as well! Describe what you read that seemed wrong, what you expected to find but didn't, or any other roadbump you ran into and we'll work up an improvement or addition.

## Relay style

Before you write any nontrivial documentation, please read through these style guidelines.

- When we refer to the product, use a capital-R Relay; when referring to the command-line tool use the backtick code `relay`. Only the initial mention at top-level docs like the Overview (or this README) needs the "Puppet Relay" moniker.
- Use short, action-oriented sentences that position Relay as the "actor". For example, "Relay stores secrets for you" is better than "Your secrets are stored in Relay" because it both uses an action verb and refers to the service as a distinct entity. (Apple's style for in-product copy on iPhone and Mac is the precedent here: "iPhone needs your password to enable TouchID after a restart")
- Workflow examples should be valid and complete (if trivial). Rather than single-line snippets, use inline code with backticks until there's enough context to provide a running workflow.
- Provide alt text with a meaningful textual description of each image for accessibility. "Screenshot of run UI" is bad alt text; "On the workflow page, there is a list of each completed run" is much better.
- Avoid overuse of "NOTE" or "CAUTION" callouts; they tend to fatigue readers and are usually redundant.
