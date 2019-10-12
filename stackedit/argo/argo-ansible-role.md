<!--
## Sections
### Heading
### Intro paragraph

	This post is about: topic
	It covers: headings
	When you finish this post, you will: most import thing
	This post will be most interesting to: intended audiences

This post will be most interesting to:
-->

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

### Workflow

I don't want to get into too much details in this blog post for the sake of clarity, since this post is dedicated to Argo provisioning, really. However, think of a `Workflow` as any other resource that you can create, list, update, delete and watch.

As an example:

```yaml
# @file wf-dag-diamond.yaml

apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: dag-diamond-
spec:
  entrypoint: diamond
  templates:
  - name: echo
    inputs:
      parameters:
      - name: message
    container:
      image: alpine:3.7
      command: [echo, "{{inputs.parameters.message}}"]
  - name: diamond
    dag:
      tasks:
      - name: A
        template: echo
        arguments:
          parameters: [{name: message, value: A}]
      - name: B
        dependencies: [A]
        template: echo
        arguments:
          parameters: [{name: message, value: B}]
      - name: C
        dependencies: [A]
        template: echo
        arguments:
          parameters: [{name: message, value: C}]
      - name: D
        dependencies: [B, C]
        template: echo
        arguments:
          parameters: [{name: message, value: D}]
```

The `templates` then define the *steps* (or *tasks* in terms of the DAG) to be executed and *how* they should be executed.
Once you have that Workflow template written and Argo up and running in your cluster, you can then make use of the Argo CLI and just issue:

```
argo submit --watch wf-dag-diamond.yaml
argo list

# or list Workflow resource the way you're used to
kubectl get wf
```


<!-- 2] SO WHAT? -->

## Provisioning

<!-- 1) What change have you made? -->
The Argo deployment can now be automated and customized to ones needs.

<!-- 2) What new capabilities have been developed? -->
It can be annoying to separate the resources that have to be deployed by cluster-admin from those that namespaces admins can create. The role makes it easy.

<!-- 3) What new connections have been made? -->
/

<!-- 4) What is true now that wasn't true before? -->
Argo can now be seamlessly deployed into an OpenShift namespace (tested on OpenShift 3.11).

<!-- 5) What do you know now that you didn't know before? -->
You are now familiar with the requirements to deploy Argo to a namespace and the resources that must be present in advance and the RBAC required.

### How are you / your team / your project / Red Hat / the world better than before?
We can now provision / deprovision Argo in a clean namespace in seconds and be up and running in seconds.

<!-- 3] NOW WHAT? -->

<!-- 1) How would I develop the subject of the post further from here? -->

<!-- 2) What is the next logical improvement that I could make in the same direction? -->
Current role provides Argo namespace-level installation. Since Argo itself also provides cross-namespace scheduling (on the Cluster level), the role might also be able to take care of that scenario

<!-- 3) What does the thing you've just done set you up to do in the future? -->
/

<!-- 4) How can the reader verify what you've just shown, and apply it to their own situation? -->
Probably the easiest solution is to set up your local minishift / minikube cluster to test the deployment on.

<!-- 5) How can the reader help you? -->
Since the deployment has been made on OpenShift 3.11, it might happen that some difficulties occur on minikube or other OpenShift versions. Any input/feedback in this direction is greatly appreciated

<!-- References -->
[Argo]: https://github.com/argoproj/argo
[Tekton]: https://github.com/tektoncd/pipeline