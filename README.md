# Tekton POC

NOTE: work done on fedora 33

## Installation

```bash
# deploys latest version
kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml

# checks if pods are running
kubectl get pods --namespace tekton-pipelines -w

# installs tekton cli (tkn)
dnf install https://github.com/tektoncd/cli/releases/download/v0.18.0/tektoncd-cli-0.18.0_Linux-64bit.rpm
```

## Usage

### Simple Task

A task can run N steps to accomplish something on a given linux container image.

Define a hello-world task (using default namespace):

```bash
# creates a task
kubectl apply -f tasks/hello-world.yaml

# checks task definition
tkn task start hello --dry-run

# runs a task
tkn task start hello

# see task logs
tkn taskrun logs $POD -f -n default

# you can also see the task pod
kubectl get po
```

Task pods are executed  on whatever namespace we want.

### Simple Pipeline

A pipeline can use existent tasks as reference so you can chain tasks together into a single workflow.

This enforces re-usable task pattern.

This will run two tasks "hello" and "goodbye", "goodbye" being triggered when "hello" is done

Create a goodbye task:

```
kubectl apply -f tasks/goodbye.yaml
```

Create a pipeline:

```bash
kubectl apply -f pipelines/hello-goodbye.yaml
```

Run the pipeline:

```bash
tkn pipeline start hello-goodbye
```

## Dashboard

Installing:

```
kubectl apply --filename https://github.com/tektoncd/dashboard/releases/latest/download/tekton-dashboard-release.yaml
```

A dashboard pod should be running in the "tekton-pipelines" namespace.

### Minikube

Start minikube proxy:

```
kubectl proxy --port=8080
```

Tekton UI should be available at http://localhost:8080/api/v1/namespaces/tekton-pipelines/services/tekton-dashboard:http/proxy/

## Personal Notes

* Tekton needs cluster level access
* Deployment was fast
* Low learning curve
* Pod is deployed faster than jenkins -> kubernetes
* Pipeline resource provides re-usable task workflow
* Lack of plugins makes you depend on static task definition
* A upstream catalog of tasks and pipelines is provided
* UI looks ok
* Task managemet is easier since it is all kubernetes declarative yaml files
* A cli is provided for convinience (there nothing that can't be done without it)
* I couldn't find a way to run periodically tasks (every 1 hour or so)
  * A workaround would to have a kubernetes pod job to executer a tekton trigger
* Triggers look very interesting, you can even expose those for external apps

## License

Consider it public domain.
