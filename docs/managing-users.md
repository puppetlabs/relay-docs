# Managing Users

## Role Based Access Control (RBAC)
Role based access control provides the ability to manage access to workflows, connections, approvals, and secrets.

A user can be one of four role types: Viewer, Operator, Approver, or Administrator. Each role provides cumulative powers, so a user should have the least-privilege role to get their job done.

### Viewer
A user with Viewer access can view workflows and run details only.

### Operator
In addition to permissions granted in Viewer role, an Operator can run workflows.

### Approver
In addition to permissions granted in Operator role, an Approver can also approve or reject approval steps.

### Administrator
In addition to permissions granted in Approver role, an Administrator can add or modify new workflows, add or modify connections, add or modify secrets, and invite and manage users.

## Inviting new users
You can invite new users to your Relay account by clicking "Access Control" from the left navigation. Enter an email address, the user's name, and the access level you want them to have. Invited users will receive an email invitation to confirm their account and create a password.

![Invite new users to relay](images/invite-users.gif)

## User management

Once a user activates their Relay account, you'll see their account name in the user table. From here, you can click on a name to assign them a different role or delete their account completely.