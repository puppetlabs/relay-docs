# Managing Users

## Role Based Access Control (RBAC)
Role based access control provides the ability to manage access to workflows, connections, approvals, and secrets. 

A user can be one of four role types: 
- **VIEWER** 
- **OPERATOR** 
- **APPROVER** 
- **ADMINISTRATOR** 

### Viewer
A user with Viewer access can view workflows and run details only. 

### Operator
In addition to permissions granted in Viewer role, an Operator can run workflows.

### Approver
In addition to permissions granted in Operator role, an Approver can also approve or reject approval steps. 

### Administrator
In addition to permissions granted in Approver role, an Administrator can add or modify new workflows, add or modify connections, add or modify secrets, and invite and manage users. 

## Inviting new users 
You can invite new users to your Relay account by clicking "Access Control" from the left navigation. Invited users will receive an email invitation to confirm their email and create a password. 

![Invite new users to relay](images/invite-users.gif)