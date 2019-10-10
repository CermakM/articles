
## What?
### What problem are you trying to solve?
Having a reproducible and configurable Argo deployment strategy.

### What led you to this point? Story?
Repetitive deployments.
Customization and configuration required.
Editing resources from the command line.

### Who else was involved?
Team.
Slack.

### What else did you try/has been tried before?
Deploying by hand, editing resources from the command line was not fun.

## So what?

### What change have you made?
The Argo deployment can now be automated and customize to ones needs.

### What new capabilities have been developed?
It can be annoying to separate the resources that have to be deployed by cluster-admin from those that namespaces admins can create. The role makes it easy.

### What new connections have been made?
/

### What is true now that wasn't true before?
Argo can now be seamlessly deployed into an OpenShift namespace (tested on OpenShift 3.11).

### What do you know now that you didn't know before?
You are now familiar with the requirements to deploy Argo to a namespace and the resources that must be present in advance and the RBAC required.

### How are you / your team / your project / Red Hat / the world better than before?
We can now provision / deprovision Argo in a clean namespace in seconds and be up and running in seconds.

## Now what?

### How would I develop the subject of the post further from here?

### What is the next logical improvement that I could make in the same direction?
Current role provides Argo namespace-level installation. Since Argo itself also provides cross-namespace scheduling (on the Cluster level), the role should also be able to take care of that.

###

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbODUzOTc0NzQ2LC0yMTMzNzIxNTE1LDczMD
k5ODExNl19
-->