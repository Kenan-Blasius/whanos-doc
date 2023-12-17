# Deploy your apps

For you to start deploying your apps, you have to set up your repo, so it can be properly understood and deploy by the build pipeline.

## Whanos.yml

To do that, you have to create a `whanos.yml` file at the root of your repository.

The `whanos.yml` file can contain a `deployment` root property, which itself can contain:

- `replicas` -> Number of replicas to have (default: 1; 2 replicas means that 2 instances of the resulting pod
must be running at the same time in the cluster);
- `resources` -> Resource needs, corresponding to [Kubernetes’ own resource specifications](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-requests-and-limits-of-pod-and-container) (default: no specifications; the syntax expected here is the same as the given link);
- `ports` -> An integer list of ports needed by the container to be forwarded to it (default: no ports forwarded).

Here is an example of a `whanos.yml` file

```yml
deployment:
    replicas: 3
    resources:
        limits:
            memory: "128M"
        requests:
            memory: "64M"
    ports:
        - 3000
```

## Accessing the app from outside the cluster

- Link the project in Jenkins (see [Link Project](https://kenan-blasius.github.io/whanos-doc/usage/jenkins#link-project))
- Once the Jenkins jobs are finished, you can connect to one of the Kubernetes VMs
- Run `sudo kubectl get svc` to find the port on which the application is listening
- Run `sudo kubectl get pods -l app=YOURAPPNAME -o=jsonpath='{range .items[*]}{.status.hostIP}{"\n"}{end}'` to get the IP address of the VM on which the application is running (replace YOURAPPNAME with the name of your application)

![image](../images/KubernetesGetPods.png)

- Go to the IP address of the VM on which the application is running on the port you found earlier
- You should see your application

![image](../images/KubernetesApp.png)
