# Configuration (18%)
## Docker commands - A Recap
![](imgs/1.jpg)
<hr/>
![](imgs/2.jpg)
<hr/>
![](imgs/3.jpg)
<hr/>
![](imgs/4.jpg)
<hr/>
![](imgs/5.jpg)
<hr/>
![](imgs/6.jpg)
<hr/>
![](imgs/7.jpg)

## Commands and Arguments in K8s PODs

![](imgs/8.jpg)
<hr/>
![](imgs/9.jpg)
<hr/>
![](imgs/10.jpg)

### Note:

A quick note on editing PODs and Deployments
#### Edit a POD
Remember, you CANNOT edit specifications of an existing POD other than the below.

- spec.containers[*].image

- spec.initContainers[*].image

- spec.activeDeadlineSeconds

- spec.tolerations

For example you cannot edit the environment variables, service accounts, resource limits (all of which we will discuss later) of a running pod. But if you really want to, you have 2 options:

1. Run the kubectl edit pod <pod name> command.  This will open the pod specification in an editor (vi editor). Then edit the required properties. When you try to save it, you will be denied. This is because you are attempting to edit a field on the pod that is not editable.



A copy of the file with your changes is saved in a temporary location as shown above.

You can then delete the existing pod by running the command:

```
kubectl delete pod webapp
```



Then create a new pod with your changes using the temporary file

    kubectl create -f /tmp/kubectl-edit-ccvrq.yaml



2. The second option is to extract the pod definition in YAML format to a file using the command

    kubectl get pod webapp -o yaml > my-new-pod.yaml

Then make the changes to the exported file using an editor (vi editor). Save the changes

    vi my-new-pod.yaml

Then delete the existing pod

    kubectl delete pod webapp

Then create a new pod with the edited file

    kubectl create -f my-new-pod.yaml



#### Edit Deployments
With Deployments you can easily edit any field/property of the POD template. Since the pod template is a child of the deployment specification,  with every change the deployment will automatically delete and create a new pod with the new changes. So if you are asked to edit a property of a POD part of a deployment you may do that simply by running the command

    kubectl edit deployment my-deployment

## Environment variables in K8s

![](imgs/11.jpg)
<hr/>
![](imgs/12.jpg)

## Configmaps
- When you have a lot of POD definition files, it gets difficult to manage the environment data in all those files.
- We can take this information out of those files and manage them separately using **configmaps**
- Configmaps are used to pass configuration data using key value pairs.
- When a POD is created, inject the configmap into the POD so the key-vaule pairs are available to the environment variables inside the POD.
- There are 2 phases involved in configuring ConfigMaps.
  - Create the ConfigMap
  - Inject them into the POD.
- Just like any other K8s object, there are 2 ways to create a ConfigMap
  - Imperative way without using a file.
  - Declarative way using a definition file.

![](imgs/13.jpg)
<hr/>
![](imgs/14.jpg)
<hr/>
![](imgs/15.jpg)
<hr/>
![](imgs/16.jpg)
<hr/>
![](imgs/17.jpg)
<hr/>
![](imgs/18.jpg)

## Secrets

![](imgs/20.jpg)
<hr/>
![](imgs/21.jpg)
<hr/>
![](imgs/22.jpg)
<hr/>
![](imgs/23.jpg)
<hr/>
![](imgs/24.jpg)
<hr/>
![](imgs/25.jpg)
<hr/>
![](imgs/26.jpg)
<hr/>
![](imgs/27.jpg)
<hr/>
![](imgs/28.jpg)
<hr/>
![](imgs/29.jpg)

## Security Contexts

![](imgs/30.jpg)


## Service Accounts

![](imgs/31.jpg)
<hr/>
![](imgs/32.jpg)
<hr/>
![](imgs/33.jpg)

- When a SA is created, K8s creates a SA object and then generates a token.
- It then generates a secret and stores the token into this secret object.
- To view the token, view the secret object by running `kubectl describe <secret>`.
- This token can then be used to make a REST call to the K8s API.

![](imgs/34.jpg)


## Resource Requirements

![](imgs/35.jpg)

- PODs remain in **pending** state if the nodes do not have enough resources.

![](imgs/36.jpg)

- By default, K8s assumes, each POD requires **0.5 CPU** and **256 Mi** of Memory. These are called **Resource Requests**.
- If your application needs more than the above resource, it can be requested in the definition file.

![](imgs/37.jpg)

- What does 1 count of CPU really mean?
- Lowest that we can configure CPU is **1 mi** or **0.1 CPU**.

![](imgs/39.jpg)

- Similarly, for Memory:

![](imgs/40.jpg)
<hr/>
![](imgs/41.jpg)
<hr/>
![](imgs/42.jpg)
<hr/>
- If the bug is intolerant to the taint on the human it will not land on the human.

![](imgs/44.jpg)

- On the other hand, if the bug is tolerant to the taint it will land on the human.

![](imgs/45.jpg)

- Two things that decide whether the bug will land on the person:
  - Taint on the person
  - Bugs toleration level to that taint

![](imgs/46.jpg)
<hr/>
![](imgs/47.jpg)
<hr/>
![](imgs/48.jpg)
<hr/>
![](imgs/49.jpg)
<hr/>
![](imgs/50.jpg)
<hr/>
![](imgs/51.jpg)
<hr/>
![](imgs/52.jpg)
<hr/>
![](imgs/53.jpg)
<hr/>
![](imgs/54.jpg)
<hr/>
![](imgs/55.jpg)

- Provides advanced features to limit where PODs are scheduled.

![](imgs/56.jpg)

- NotIn Operator

![](imgs/57.jpg)

- Exists operator

![](imgs/58.jpg)

- If NodeAffinity doesn't find a match for the POD, the type of NodeAffinity defines the behavior.
- Following NodeAffinity behaviors are available:
  - requiredDuringSchedulingIgnoredDuringExecution
  - preferredDuringSchedulingIgnoredDuringExecution

![](imgs/59.jpg)


# Exercises


## Configuring a Pod to Use a ConfigMap

1. Create a new file named `config.txt` with the following environment variables as key/value pairs on each line.

- `DB_URL` equates to `localhost:3306`
- `DB_USERNAME` equates to `postgres`

2. Create a new ConfigMap named `db-config` from that file.
3. Create a Pod named `backend` that uses the environment variables from the ConfigMap and runs the container with the image `nginx`.
4. Shell into the Pod and print out the created environment variables. You should find `DB_URL` and `DB_USERNAME` with their appropriate values.

<details><summary>Show Solution</summary>
<p>

Create the environment variables in the text file.

```bash
$ echo -e "DB_URL=localhost:3306\nDB_USERNAME=postgres" > config.txt
```

Create the ConfigMap and point to the text file upon creation.

```bash
$ kubectl create configmap db-config --from-env-file=config.txt
configmap/db-config created
$ kubectl run backend --image=nginx --restart=Never -o yaml --dry-run > pod.yaml
```

The final YAML file should look similar to the following code snippet.

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: backend
  name: backend
spec:
  containers:
  - image: nginx
    name: backend
    envFrom:
      - configMapRef:
          name: db-config
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

Create the Pod by pointing the `create` command to the YAML file.

```bash
$ kubectl create -f pod.yaml
```

Log into the Pod and run the `env` command.

```bash
$ kubectl exec backend -it -- /bin/sh
/ # env
DB_URL=localhost:3306
DB_USERNAME=postgres
...
/ # exit
```

</p>
</details>

## Configuring a Pod to Use a Secret

1. Create a new Secret named `db-credentials` with the key/value pair `db-password=passwd`.
2. Create a Pod named `backend` that defines uses the Secret as environment variable named `DB_PASSWORD` and runs the container with the image `nginx`.
3. Shell into the Pod and print out the created environment variables. You should find `DB_PASSWORD` variable.

<details><summary>Show Solution</summary>
<p>

It's easy to create the secret from the command line. Furthermore, execute the `run` command to generate the YAML file for the Pod.

```bash
$ kubectl create secret generic db-credentials --from-literal=db-password=passwd
secret/db-credentials created
$ kubectl get secrets
NAME              TYPE      DATA   AGE
db-credentials    Opaque    1      26s
$ kubectl run backend --image=nginx --restart=Never -o yaml --dry-run > pod.yaml
```

Edit the YAML file and create an environment that reads the relevant key from the secret.

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: backend
  name: backend
spec:
  containers:
  - image: nginx
    name: backend
    env:
      - name: DB_PASSWORD
        valueFrom:
          secretKeyRef:
            name: db-credentials
            key: db-password
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

Create the Pod by pointing the `create` command to the YAML file.

```bash
$ kubectl create -f pod.yaml
```

You can find the environment variable by shelling into the container and running the `env` command.

```
$ kubectl exec -it backend -- /bin/sh
/ # env
DB_PASSWORD=passwd
/ # exit
```

</p>
</details>

## Creating a Security Context for a Pod

1. Create a Pod named `secured` that uses the image `nginx` for a single container. Mount an `emptyDir` volume to the directory `/data/app`.
2. Files created on the volume should use the filesystem group ID 3000.
3. Get a shell to the running container and create a new file named `logs.txt` in the directory `/data/app`. List the contents of the directory and write them down.

<details><summary>Show Solution</summary>
<p>

Start by creating the Pod definition as YAML file.

```bash
$ kubectl run secured --image=nginx --restart=Never -o yaml --dry-run > secured.yaml
```

Edit the YAML file, add a volume and a volume mount. Add a security context with the relevant group ID.

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: secured
  name: secured
spec:
  securityContext:
    fsGroup: 3000
  containers:
  - image: nginx
    name: secured
    volumeMounts:
    - name: data-vol
      mountPath: /data/app
    resources: {}
  volumes:
  - name: data-vol
    emptyDir: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

Create the Pod and log into the container. Create the file in the directory of the volume mount. The group ID should be 3000 as defined by the security context.

```bash
$ kubectl create -f secured.yaml
pod/secured created
$ kubectl exec -it secured -- sh
/ # cd /data/app
/ # touch logs.txt
/ # ls -l
-rw-r--r-- 1 root 3000 0 Mar 11 15:56 logs.txt
/ # exit
```

</p>
</details>

## Defining a Pod’s Resource Requirements

Create a resource quota named `apps` under the namespace `rq-demo` using the following YAML definition in the file `rq.yaml`.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: app
spec:
  hard:
    pods: "2"
    requests.cpu: "2"
    requests.memory: 500m
```

1. Create a new Pod that exceeds the limits of the resource quota requirements. Write down the error message.
2. Change the request limits to fulfill the requirements to ensure that the Pod could be created successfully. Write down the output of the command that renders the used amount of resources for the namespace.

<details><summary>Show Solution</summary>
<p>

First create the namespace and the resource quota in the namespace.

```bash
$ kubectl create namespace rq-demo
$ kubectl create -f rq.yaml --namespace=rq-demo
resourcequota/app created
$ kubectl describe quota --namespace=rq-demo
Name:            app
Namespace:       rq-demo
Resource         Used  Hard
--------         ----  ----
pods             0     2
requests.cpu     0     2
requests.memory  0     500m
```

Next, create the YAML file named `pod.yaml` with more requested memory than available in the quota.

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: mypod
  name: mypod
spec:
  containers:
  - image: nginx
    name: mypod
    resources:
      requests:
        memory: "1G"
        cpu: "400m"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

Create the Pod and observe the error message.

```bash
$ kubectl create -f pod.yaml --namespace=rq-demo
Error from server (Forbidden): error when creating "pod.yaml": pods "mypod" is forbidden: exceeded quota: app, requested: requests.memory=1G, used: requests.memory=0, limited: requests.memory=500m
```

Lower the memory settings to less than `500m` (e.g. `200m`) and create the Pod.

```bash
$ kubectl create -f pod.yaml --namespace=rq-demo
pod/mypod created
$ kubectl describe quota --namespace=rq-demo
Name:            app
Namespace:       rq-demo
Resource         Used  Hard
--------         ----  ----
pods             1     2
requests.cpu     400m  2
requests.memory  200m  500m
```

</p>
</details>

## Using a Service Account

1. Create a new service account named `backend-team`.
2. Print out the token for the service account in YAML format.
3. Create a Pod named `backend` that uses the image `nginx` and the identity `backend-team` for running processes.
4. Get a shell to the running container and print out the token of the service account.

<details><summary>Show Solution</summary>
<p>

First, create the service acccount and inspect it.

```bash
$ kubectl create serviceaccount backend-team
serviceaccount/backend-team created
$ kubectl get serviceaccount backend-team -o yaml --export
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: 2019-05-09T22:43:54Z
  name: backend-team
  namespace: default
  resourceVersion: "1888067"
  selfLink: /api/v1/namespaces/default/serviceaccounts/backend-team
  uid: ecd3b7ea-72ab-11e9-96c5-025000000001
secrets:
- name: backend-team-token-hskch
```

Next, you can create a new Pod and assign the service account to it.

```bash
$ kubectl run backend --image=nginx --restart=Never --serviceaccount=backend-team
```

You can print out the token from the volume source at `/var/run/secrets/kubernetes.io/serviceaccount`.

```bash
$ kubectl exec -it backend -- /bin/sh
/ # cat /var/run/secrets/kubernetes.io/serviceaccount/token
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImJhY2tlbmQtdGVhbS10b2tlbi1kbTJmZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJiYWNrZW5kLXRlYW0iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIxNzM0MzVjMS00NDJmLTExZTktOGRjMy0wMjUwMDAwMDAwMDEiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDpiYWNrZW5kLXRlYW0ifQ.DjWUxEMNUmQVoXd4b-eIjxboj3w3k7hS5hfV8mm8eoEPz3HJJMgjIpAaurcvo1pp2Ggpd1kIhQvfRqI6-u57f80N5UqXt_qATJfonat2NNXX8pXmFNoPig9LB-pbo8TN_pYGWNworXsxmK9w6V9eaRosIinRp0u-cvijQbsBw3lxWgGo9S4G-7f19mMKN1Pg2xS2J6fKX9IKvhHrUkM91nwcwmsO0use5B4TGbuRa9METiGsfEpegvzMPBbPl0B_T1ANH_pck0LFNtvKe0g1v5zpKx2lRF9WdFAqPsG7BJ1dEH88JtBHzD59OhxIPqtyT4sXKjACBN_ka5ZADMzPJg
```

</p>
</details>
