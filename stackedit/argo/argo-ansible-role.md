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

<p align="center">
	<img src="https://miro.medium.com/max/963/1*ZKFG3dbNO3S646rM1BFkrw.png" />
	<span style="font-size:small;">
		Credit: <a href="https://blog.argoproj.io">argoproj.io</a>
	</span>
</p>

Even though this isn't an article about Argo, neither shall I explain the process that lead to the choice of Argo over other solutions that we'd considered (of which [Tekton] is very well worth mentioning), I will still vaguely describe the purpose and nature of Argo.

As [Argo] developers puts it themselves: "Argo Workflows is an open source container-native workflow engine for orchestrating parallel jobs on Kubernetes. Argo Workflows is implemented as a Kubernetes CRD (Custom Resource Definition)." Each workflows is then composed of one or more steps, where each step leads to a pod being created in the cluster. Steps can be arranged in a sequence or in a directed acyclic graph (DAG) which allows for an easy orchestration and parallelisation of jobs on top of Kubernetes. In fact, not only jobs, but also *resources* can be orchestrated, which makes Argo very unique and very fit for our use case. There are plenty of other features that Argo natively support, I encourage you to read about them in [the project readme](https://github.com/argoproj/argo#features).

### Workflow

I don't want to get into too much details in this blog post for the sake of clarity, since this post is dedicated to Argo provisioning, really. However, think of a `Workflow` as any other resource that you can create, list, delete, ..., and watch.

As an example:

```yaml
# @file wf-dag-diamond.yaml
---
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

```bash
argo submit --watch wf-dag-diamond.yaml
argo list

# or list Workflow resource the way you're used to
kubectl get wf
```


<!-- 2] SO WHAT? -->

## Provisioning

<!-- 1) What change have you made? -->
I am quite certain that we can agree that we — the lazy humans — do not want to spend our time doing things that don't make sense to us and/or are unnecessarily complicated. As such, we tend to make things as simple as possible (which in fact turns out not to be the case eventually, despite our intentions being pure) and we like to automate the boring stuff. 
Even though Argo developers has made it as easy as possible to deploy the basic Argo installation. Having an opportunity to provision Argo with a specific configuration, additional set of manifests, customized labels or production-level overlays is something worth investing the time in, don't you think?

Let's install the [role](https://galaxy.ansible.com/cermakm/argo_workflows) before we proceed further. Make sure you have `Ansible>=2.4` [installed](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) and install the role from [Ansible Galaxy](https://docs.ansible.com/ansible/latest/reference_appendices/galaxy.html#installing-roles).

```
ansible-galaxy install cermakm.argo_workflows
```

<!-- 2) What new capabilities have been developed? -->
### Provisioning as cluster-admin or developer
The [Ansible role for Argo Workflows](https://github.com/CermakM/ansible-role-argo-workflows) introduces a hierarchy of configuration options. Starting with the division by **deployment privileges**.

The role accepts the `as` argument which is used to determine what resources should be created in a given `namespace` (which is another configurable). In a [multi-tenant](https://cloud.google.com/kubernetes-engine/docs/concepts/multitenancy-overview) architecture which is typical for Kubernetes (or OpenShift, for that matter), there is a decent chance that the RBAC policy does not grant you the privileges to create resources on a cluster-level. In fact, this is the default behaviour on OpenShift. Therefore, it might be a good idea to attempt to deploy only the resources that you actually *can* create and let cluster-admins handle the rest in advance (they usually only need to do that once for a new namespace).

<p align="center">
	<img src="https://cloud.google.com/kubernetes-engine/images/enterprise-multitenancy.svg" />
</p>

The role by default assumes `as=cluster-admin` and `namespace=argo`. As developer, just pass in `as=developer` to the role and let it handle the rest.

From a real-life scenario, our current organization provides a development cluster in which the cluster admins have created namespaces for teams to use. I, as a regular developer, haven't got the cluster-admin role and therefore I cannot, for example, create CRDS. As such, I request those CRDs to be added to the cluster and a `Role` created in my namespace for me to be able to manipulate the CRs (the role creates all of these when run `as=cluster-admin`). From now on, I am able to provision and deprovision the rest of the application myself `as=developer` using the Ansible role.

```bash
# make sure to replace provision.yaml with the name of your provisioning playbook
ansible-playbook provision.yaml --e as=developer
```

> NOTE: The role currently uses a 'namespace installation' of Argo, which means that all resources are namespaced and you're not allowed, for example, to schedule workflows in different namespaces.

### Overlays

### Additional configuration


<!-- 3) What new connections have been made? -->
<!-- - -->

<!-- 4) What is true now that wasn't true before? -->

<!-- 5) What do you know now that you didn't know before? -->

<!-- 6) How are you / your team / your project / Red Hat / the world better than before? -->

<!-- 3] NOW WHAT? -->
## What now?

<!-- 1) How would I develop the subject of the post further from here? -->
<!-- - -->

<!-- 2) What is the next logical improvement that I could make in the same direction? -->
Current role provides a namespace-level installation. Since Argo itself also offers cross-namespace installation to allow scheduling on the cluster level, the role might also be able to take care of that scenario in the future.

<!-- 3) What does the thing you've just done set you up to do in the future? -->
<!-- - -->


<!-- 4) How can the reader verify what you've just shown, and apply it to their own situation? -->
<!-- 5) How can the reader help you? -->
Probably the easiest solution to try all of the things described above is to set up your local [minishift] / [minikube] cluster and take the Ansible role for a spin. It's been tested on OpenShift 3.11 so far, hence it is our sincere hope that it also runs without any issues in other environments. It might happen that some difficulties occur on minikube or other environments. Any input/feedback in this direction is greatly appreciated.

Good luck and happy provisioning!

<!-- References -->
[Argo]: https://github.com/argoproj/argo
[Tekton]: https://github.com/tektoncd/pipeline

[minishift]: https://www.okd.io/minishift/
[minikube]: https://github.com/kubernetes/minikube