
<!-- 1] WHAT? -->

<!-- 1) What problem are you trying to solve? -->

Recently, a need for some sort of a workflow management on top of Kubernetes has arisen in our team due to various reasons — the architecture has become so convoluted that it is now hard to manage all of its components — and [Argo]'s been picked as the engine of choice.

<!-- 2) What led you to this point? Story? -->

[Argo] being a configurable beast who can do a lot of good when treated well has a non-trivial provisioning. In order for Argo to fit our needs, the deployment had to be slightly adjusted (more to that later) and the configuration tweaked as well.
As such, a reproducible deployment strategy for both Kubernetes and OpenShift and configurable provisioning has been a hard requirements so that we could iterate quickly on the workflow design itself.

<!-- Who else was involved? -->
<!-- - -->

<!-- What else did you try/has been tried before? -->
<!-- - -->

## Argo

Even though this isn't an article about Argo, neither shall I explain the process that lead to the choice of Argo over other solutions that we'd considered (of which [Tekton] is very well worth mentioning), I will still vaguely describe the purpose and nature of Argo.

As [Argo] developers puts it themselves: "Argo Workflows is an open source container-native workflow engine for orchestrating parallel jobs on Kubernetes. Argo Workflows is implemented as a Kubernetes CRD (Custom Resource Definition)." Each workflows is then composed of one or more steps, where each step leads to a pod being created in the cluster. Steps can be arranged in a sequence or in a directed acyclic graph (DAG) which allows for an easy orchestration and parallelisation of jobs on top of Kubernetes. In fact, not only jobs, but also *resources* can be orchestrated, which makes Argo very unique and very fit for our use case. There are plenty of other features that Argo natively support, I encourage you to read about them in [the project readme](https://github.com/argoproj/argo#features).



<!-- 2] SO WHAT? -->

### What change have you made?
The Argo deployment can now be automated and customized to ones needs.

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

<!-- 3] NOW WHAT? -->

### How would I develop the subject of the post further from here?

### What is the next logical improvement that I could make in the same direction?
Current role provides Argo namespace-level installation. Since Argo itself also provides cross-namespace scheduling (on the Cluster level), the role might also be able to take care of that scenario

### What does the thing you've just done set you up to do in the future?
/

### How can the reader verify what you've just shown, and apply it to their own situation?
Probably the easiest solution is to set up your local minishift / minikube cluster to test the deployment on.

### How can the reader help you?
Since the deployment has been made on OpenShift 3.11, it might happen that some difficulties occur on minikube or other OpenShift versions. Any input/feedback in this direction is greatly appreciated

## Sections
### Heading
### Intro paragraph

	This post is about: topic
	It covers: headings
	When you finish this post, you will: most import thing
	This post will be most interesting to: intended audiences

This post is about deploying Argo Ansible on OpenShift 3.11 using Ansible

When you finish this post, you will be able to use Ansible to deploy Argo into your own namespace at ease.

headings:
- workflow management
- argo
- ansible
- provisioning
- repetitive deployments
- rbac
- example

This post will be most interesting to:

<!-- References -->
[Argo]: https://github.com/argoproj/argo
[Tekton]: https://github.com/tektoncd/pipeline