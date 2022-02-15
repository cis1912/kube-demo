# Kube Demo

Time to toy around with Kubernetes! In this lab, we'll play around with Kube and get comfortable with some basic commands.

## Installation

[Install Kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)

[Install Kubectl](https://kubernetes.io/docs/tasks/tools/)

## Cluster Bootstrap

First, let's create our `kind` cluster and configure `kubectl` to use it:

```
$ kind create cluster --name cis188
$ kubectl config use-context kind-cis188
```

### Kube Tricks

We've provided a cheatsheet with basic guides for debugging Kubernetes on our [Kube Tricks](https://cis188.org/resources/kube_tricks/) page. Look there for help both here and on the homeworks.

## Demo

In this demo, we'll be using `kubectl` to create some resources and interact with them. As always, we'll start with 2048. First, let's manually create a pod for the 2048 image:

```
$ kubectl run lab-2048 --image=alexwhen/docker-2048 --port=80
```

Now use `kubectl get pods` to see our list of running pods. If the prior command worked, you should see the 2048 pod!

Now, use `kubectl describe pod lab-2048` to get more detailed information about our pod. Note the events on the pod - they show you information about what happened while the pod was spinning up.

### Kubernetes Actions

As you may have noticed, our Kubectl commands all follow the pattern `kubectl <verb> <resource_type> <resource_id>`. This syntax is not just a property of Kubectl; all Kubernetes actions have this structure! As we mentioned in lecture, this structure is enforced because Kubernetes is an API on which we need to enforce certain permissions. Enforcing that all actions take this form allows us to easily define which verb/resource/id combinations are legal and which aren't.

This form also makes it easy to extend Kubernetes, but that's a lecture topic for a few weeks from now.

### Connecting

While normally we port forward directly to a service, we can also forward to a pod (thanks to Armaan for teaching me this trick). Port forward and go to `localhost:8080` to play 2048:

```
$ kubectl port-forward pod/lab-2048 8080:80
```

### Using a service

As we mentioned, applications are typically exposed through a service in Kubernetes. Now, figure out the appropriate `kubectl` command to expose 2048 and then port forward to the service. [The docs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands) might be helpful.

Note that you don't need to use `kubectl create service`. Kubectl provides you an easier command to use to expose the pod.

If you've exposed the application correctly, you should be able to port forward to the service and see your application:

```
$ kubectl port-forward svc/lab-2048 8080:80
```

### Integrating with your homeworks

Now comes the fun part. Use what you've learned here to replicate the docker compose setup from homeworks. You can do this using only `kubectl run` and `kubectl expose` (and maybe a `kind` command ;)).

This is a daunting task at first, so it's best to do it in discrete steps:

1. Add FastAPI to the cluster
    - Did this work? Is there maybe a [kind command](https://kind.sigs.k8s.io/docs/user/quick-start/) to help with the image problem?
2. Expose FastAPI
3. Access the `/hello` route to check FastAPI is up
4. Add Redis to the cluster
5. Expose Redis
6. Test routes that rely on Redis
    - Is FastAPI configured to talk to Redis?

Here, it might be helpful to use `kubectl edit` to modify your applications on the fly. Do not use this in production, but for development, it can be a useful tool.

## But is this reproducible??

You probably saw this coming. While we're using Kubernetes here, we're not getting it's main value proposition. If we lost any of these commands, we wouldn't know how to get our application back. Additionally, once we create our resources, we have no idea what their config actually looks like.

In the homework for this week, you'll be doing the same stuff you're doing here but doing it all reproducibly. This means that you're always a `kubectl apply` away from having your application ready to go.

### Cleanup

You can use `kubectl delete` to delete individual resources in your cluster or `kind delete cluster --name cis188` to delete the whole cluster.
