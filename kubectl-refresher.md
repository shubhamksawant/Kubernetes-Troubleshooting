
<details>
<summary>
Senario -
You've made changes to the deployment configuration in the nginx-deployment.yml file, but you want to review the differences before applying them. Use the command to show the difference between local and cluster configurations. What has changed?

```bash


  ```
  
</summary>

```bash


controlplane ~ ✖ cat nginx-deployment.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: app
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80 

        
controlplane ~ ➜  k diff -f nginx-deployment.yml 
diff -u -N /tmp/LIVE-975054143/apps.v1.Deployment.app.nginx-deployment /tmp/MERGED-2087480864/apps.v1.Deployment.app.nginx-deployment
--- /tmp/LIVE-975054143/apps.v1.Deployment.app.nginx-deployment 2025-01-02 06:31:13.232468235 +0000
+++ /tmp/MERGED-2087480864/apps.v1.Deployment.app.nginx-deployment      2025-01-02 06:31:13.232468235 +0000
@@ -6,7 +6,7 @@
     kubectl.kubernetes.io/last-applied-configuration: |
       {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"app":"nginx"},"name":"nginx-deployment","namespace":"app"},"spec":{"replicas":3,"selector":{"matchLabels":{"app":"nginx"}},"template":{"metadata":{"labels":{"app":"nginx"}},"spec":{"containers":[{"image":"nginx:1.14.2","name":"nginx","ports":[{"containerPort":80}]}]}}}}
   creationTimestamp: "2025-01-02T06:25:57Z"
-  generation: 1
+  generation: 2
   labels:
     app: nginx
   name: nginx-deployment
@@ -15,7 +15,7 @@
   uid: d57c24b6-2bf9-4c72-b1d8-ee6b5430f7d6
 spec:
   progressDeadlineSeconds: 600
-  replicas: 3
+  replicas: 2
   revisionHistoryLimit: 10
   selector:
     matchLabels:

  ```
  </details>


<details>
<summary>
Senario -
How many pods are on the node01?

</summary>

```bash


controlplane ~ ➜  k get all -A --field-selector spec.nodeName=node01
NAMESPACE     NAME                               READY   STATUS             RESTARTS   AGE
app           myapp                              0/1     Completed          0          23m
app           nginx-deployment-d556bf558-jqc7j   1/1     Running            0          16m
app           nginx-deployment-d556bf558-v496x   1/1     Running            0          16m
app           nginx-deployment-d556bf558-x6b65   1/1     Running            0          16m
app           python-app                         1/1     Running            0          23m
default       backend                            1/1     Running            0          23m
default       webserver                          0/1     ImagePullBackOff   0          45m
kube-system   kube-proxy-926s4                   1/1     Running            0          84m
kube-system   weave-net-br9j5                    2/2     Running            0          84m
Error from server (BadRequest): Unable to find "/v1, Resource=replicationcontrollers" that match label selector "", field selector "spec.nodeName=node01": field label not supported: spec.nodeName
Error from server (BadRequest): Unable to find "/v1, Resource=services" that match label selector "", field selector "spec.nodeName=node01": field label not supported: spec.nodeName
Error from server (BadRequest): Unable to find "apps/v1, Resource=daemonsets" that match label selector "", field selector "spec.nodeName=node01": "spec.nodeName" is not a known field selector: only "metadata.name", "metadata.namespace"
Error from server (BadRequest): Unable to find "apps/v1, Resource=deployments" that match label selector "", field selector "spec.nodeName=node01": "spec.nodeName" is not a known field selector: only "metadata.name", "metadata.namespace"
Error from server (BadRequest): Unable to find "apps/v1, Resource=replicasets" that match label selector "", field selector "spec.nodeName=node01": "spec.nodeName" is not a known field selector: only "metadata.name", "metadata.namespace"
Error from server (BadRequest): Unable to find "apps/v1, Resource=statefulsets" that match label selector "", field selector "spec.nodeName=node01": "spec.nodeName" is not a known field selector: only "metadata.name", "metadata.namespace"
Error from server (BadRequest): Unable to find "autoscaling/v2, Resource=horizontalpodautoscalers" that match label selector "", field selector "spec.nodeName=node01": "spec.nodeName" is not a known field selector: only "metadata.name", "metadata.namespace"
Error from server (BadRequest): Unable to find "batch/v1, Resource=cronjobs" that match label selector "", field selector "spec.nodeName=node01": "spec.nodeName" is not a known field selector: only "metadata.name", "metadata.namespace"
Error from server (BadRequest): Unable to find "batch/v1, Resource=jobs" that match label selector "", field selector "spec.nodeName=node01": field label "spec.nodeName" not supported for Job


controlplane ~ ✖ kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=node01
NAMESPACE     NAME                               READY   STATUS             RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
app           myapp                              0/1     Completed          0          24m   10.244.0.5    node01   <none>           <none>
app           nginx-deployment-d556bf558-jqc7j   1/1     Running            0          17m   10.244.0.5    node01   <none>           <none>
app           nginx-deployment-d556bf558-v496x   1/1     Running            0          17m   10.244.0.7    node01   <none>           <none>
app           nginx-deployment-d556bf558-x6b65   1/1     Running            0          17m   10.244.0.6    node01   <none>           <none>
app           python-app                         1/1     Running            0          24m   10.244.0.4    node01   <none>           <none>
default       backend                            1/1     Running            0          24m   10.244.0.3    node01   <none>           <none>
default       webserver                          0/1     ImagePullBackOff   0          46m   10.244.0.2    node01   <none>           <none>
kube-system   kube-proxy-926s4                   1/1     Running            0          85m   192.4.122.8   node01   <none>           <none>
kube-system   weave-net-br9j5                    2/2     Running            0          85m   192.4.122.8   node01   <none>           <none>

  ```
  </details>

<details>
<summary>
Senario -
Write the logs of the frontend deployment into a single file logs.txt
Deployment can be in any of the namespaces

</summary>

```bash
controlplane ~ ➜  k logs pod/frontend-6587cbd7f5-gjw4f -n app
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2025/01/02 06:45:15 [notice] 1#1: using the "epoll" event method
2025/01/02 06:45:15 [notice] 1#1: nginx/1.27.3
2025/01/02 06:45:15 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2025/01/02 06:45:15 [notice] 1#1: OS: Linux 5.4.0-1106-gcp
2025/01/02 06:45:15 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2025/01/02 06:45:15 [notice] 1#1: start worker processes
2025/01/02 06:45:15 [notice] 1#1: start worker process 80
2025/01/02 06:45:15 [notice] 1#1: start worker process 81
2025/01/02 06:45:15 [notice] 1#1: start worker process 82
2025/01/02 06:45:15 [notice] 1#1: start worker process 83
2025/01/02 06:45:15 [notice] 1#1: start worker process 84
2025/01/02 06:45:15 [notice] 1#1: start worker process 85
2025/01/02 06:45:15 [notice] 1#1: start worker process 86
2025/01/02 06:45:15 [notice] 1#1: start worker process 87
2025/01/02 06:45:15 [notice] 1#1: start worker process 88
2025/01/02 06:45:15 [notice] 1#1: start worker process 89
2025/01/02 06:45:15 [notice] 1#1: start worker process 90
2025/01/02 06:45:15 [notice] 1#1: start worker process 91
2025/01/02 06:45:15 [notice] 1#1: start worker process 92
2025/01/02 06:45:15 [notice] 1#1: start worker process 93
2025/01/02 06:45:15 [notice] 1#1: start worker process 94
2025/01/02 06:45:15 [notice] 1#1: start worker process 95
2025/01/02 06:45:15 [notice] 1#1: start worker process 96
2025/01/02 06:45:15 [notice] 1#1: start worker process 97
2025/01/02 06:45:15 [notice] 1#1: start worker process 98
2025/01/02 06:45:15 [notice] 1#1: start worker process 99
2025/01/02 06:45:15 [notice] 1#1: start worker process 100
2025/01/02 06:45:15 [notice] 1#1: start worker process 101
2025/01/02 06:45:15 [notice] 1#1: start worker process 102
2025/01/02 06:45:15 [notice] 1#1: start worker process 103
2025/01/02 06:45:15 [notice] 1#1: start worker process 104
2025/01/02 06:45:15 [notice] 1#1: start worker process 105
2025/01/02 06:45:15 [notice] 1#1: start worker process 106
2025/01/02 06:45:15 [notice] 1#1: start worker process 107
2025/01/02 06:45:15 [notice] 1#1: start worker process 108
2025/01/02 06:45:15 [notice] 1#1: start worker process 109
2025/01/02 06:45:15 [notice] 1#1: start worker process 110
2025/01/02 06:45:15 [notice] 1#1: start worker process 111
2025/01/02 06:45:15 [notice] 1#1: start worker process 112
2025/01/02 06:45:15 [notice] 1#1: start worker process 113
2025/01/02 06:45:15 [notice] 1#1: start worker process 114
2025/01/02 06:45:15 [notice] 1#1: start worker process 115

controlplane ~ ➜  k logs pod/frontend-6587cbd7f5-gjw4f -n app >> logs.txt

  ```
  </details>

<details>
<summary>
Senario -
You just learned there’s a lifecycle spec defined in one of the pod manifests. Use the right command to learn more about that lifecycle spec. Write the output out to a file lifecycle.txt

</summary>

```bash

controlplane ~ ✖ kubectl explain pod.spec.containers.lifecycle
KIND:       Pod
VERSION:    v1

FIELD: lifecycle <Lifecycle>


DESCRIPTION:
    Actions that the management system should take in response to container
    lifecycle events. Cannot be updated.
    Lifecycle describes actions that the management system should take in
    response to container lifecycle events. For the PostStart and PreStop
    lifecycle handlers, management of the container blocks until the action is
    complete, unless the container process fails, in which case the handler is
    aborted.
    
FIELDS:
  postStart     <LifecycleHandler>
    PostStart is called immediately after a container is created. If the handler
    fails, the container is terminated and restarted according to its restart
    policy. Other management of the container blocks until the hook completes.
    More info:
    https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/#container-hooks

  preStop       <LifecycleHandler>
    PreStop is called immediately before a container is terminated due to an API
    request or management event such as liveness/startup probe failure,
    preemption, resource contention, etc. The handler is not called if the
    container crashes or exits. The Pod's termination grace period countdown
    begins before the PreStop hook is executed. Regardless of the outcome of the
    handler, the container will eventually terminate within the Pod's
    termination grace period (unless delayed by finalizers). Other management of
    the container blocks until the hook completes or until the termination grace
    period is reached. More info:
    https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/#container-hooks



controlplane ~ ➜  kubectl explain pod.spec.containers.lifecycle >> lifecycle.txt


  ```
  </details>

<details>
<summary>
Senario -
You are investigating an incident that happened on the pod called myapp and need to ensure logs display timestamps for accurate analysis. Write the logs to a file timestamps.txt
Deployment can be in any of the namespaces

</summary>

```bash

controlplane ~ ✖ k logs pod/myapp -n app --timestamps
2025-01-02T06:19:28.235632774Z map[id:1 message:message 1]
2025-01-02T06:19:28.335856568Z map[id:2 message:message 2]
2025-01-02T06:19:28.436112331Z map[id:1 message:message 1]
2025-01-02T06:19:28.536340753Z map[id:1 message:message 1]
2025-01-02T06:19:28.635609916Z map[id:2 message:message 2]
2025-01-02T06:19:28.736240361Z map[id:1 message:message 1]
2025-01-02T06:19:28.835389952Z map[id:2 message:message 2]
2025-01-02T06:19:28.935780289Z map[id:1 message:message 1]
2025-01-02T06:19:29.036010604Z map[id:2 message:message 2]
2025-01-02T06:19:29.136174581Z map[id:2 message:message 2]
2025-01-02T06:19:29.235405328Z map[id:1 message:message 1]
2025-01-02T06:19:29.335750395Z map[id:3 message:message 3]
2025-01-02T06:19:29.436003622Z map[id:1 message:message 1]
2025-01-02T06:19:29.536251200Z map[id:2 message:message 2]
2025-01-02T06:19:29.635384325Z map[id:3 message:message 3]
2025-01-02T06:19:29.735793969Z map[id:2 message:message 2]
2025-01-02T06:19:29.835968425Z map[id:3 message:message 3]
2025-01-02T06:19:29.936274582Z map[id:1 message:message 1]
2025-01-02T06:19:30.035410156Z map[id:3 message:message 3]
2025-01-02T06:19:30.135819581Z map[id:3 message:message 3]
2025-01-02T06:19:30.236137405Z map[id:3 message:message 3]
2025-01-02T06:19:30.335312844Z map[id:2 message:message 2]
2025-01-02T06:19:30.435682972Z map[id:2 message:message 2]
2025-01-02T06:19:30.535928111Z map[id:2 message:message 2]
2025-01-02T06:19:30.636195220Z map[id:2 message:message 2]
2025-01-02T06:19:30.735319269Z map[id:3 message:message 3]
2025-01-02T06:19:30.835612083Z map[id:2 message:message 2]
2025-01-02T06:19:30.935787250Z map[id:3 message:message 3]
2025-01-02T06:19:31.036094688Z map[id:1 message:message 1]
2025-01-02T06:19:31.136343276Z map[id:1 message:message 1]
2025-01-02T06:19:31.235884822Z map[id:2 message:message 2]
2025-01-02T06:19:31.336128355Z map[id:1 message:message 1]
2025-01-02T06:19:31.436379901Z map[id:3 message:message 3]
2025-01-02T06:19:31.535782371Z map[id:3 message:message 3]
2025-01-02T06:19:31.636044217Z map[id:1 message:message 1]
2025-01-02T06:19:31.736283876Z map[id:1 message:message 1]
2025-01-02T06:19:31.835607750Z map[id:3 message:message 3]
2025-01-02T06:19:31.935809877Z map[id:1 message:message 1]
2025-01-02T06:19:32.036060978Z map[id:1 message:message 1]
2025-01-02T06:19:32.136343180Z map[id:2 message:message 2]
2025-01-02T06:19:32.235671030Z map[id:3 message:message 3]
2025-01-02T06:19:32.337241559Z map[id:2 message:message 2]
2025-01-02T06:19:32.435459750Z map[id:1 message:message 1]
2025-01-02T06:19:32.535776039Z map[id:1 message:message 1]
2025-01-02T06:19:32.636035118Z map[id:3 message:message 3]
2025-01-02T06:19:32.736289072Z map[id:1 message:message 1]
2025-01-02T06:19:32.835711112Z map[id:3 message:message 3]
2025-01-02T06:19:32.935864355Z map[id:2 message:message 2]
2025-01-02T06:19:33.036036220Z map[id:3 message:message 3]
2025-01-02T06:19:33.136359542Z map[id:1 message:message 1]

controlplane ~ ➜  k logs pod/myapp -n app --timestamps >> timestamps.txt


  ```
  </details>

<details>
<summary>
Senario -

An incident occurred in the last hour in python-app. Use the appropriate commands to investigate and gather information on logs during that time frame. Write the output to the last-hour.txt file.


Store the command used to get logs of the last hour in /root/q9.txt

We will validate your answer based on the command stored in /root/q9.txt
</summary>

```bash

controlplane ~ ➜  kubectl logs -n app python-app --since=1h > last-hour.txt

controlplane ~ ➜  echo "kubectl logs -n app python-app --since=1h > last-hour.txt" > q9.txt

  ```
  </details>

<details>
<summary>
Senario -
An incident occurred in the last hour in python-app. Use the appropriate commands to investigate and gather information on logs during that time frame. Write the output to the last-hour.txt file.
Store the command used to get logs of the last hour in /root/q9.txt
We will validate your answer based on the command stored in /root/q9.txt
</summary>

```bash
Use stream option of the logs command to get immediate feedback

kubectl logs -n app python-app -f

  ```
  </details>

<details>
<summary>
Senario -
Which of the following actions can be performed using kubectl debug? Choose all valid answers:

A) Running a new pod in the node’s host namespaces with access to its filesystem.
B) Attaching a container to a running pod without restarting the pod.
C) Copy files from a local machine to a pod.
D) Create a new pod with a modified container image for debugging.

</summary>

```bash

A) Running a new pod in the node’s host namespaces with access to its filesystem.
B) Attaching a container to a running pod without restarting the pod.
D) Create a new pod with a modified container image for debugging.

  ```
  </details>

<details>
<summary>
Senario -
When are ephemeral containers useful?

A) Container is starting up
B) When using distroless images
C) Debugging a pending pod
D) Images doesn’t contain debugging utilities
E) Debugging container in a crashloop

</summary>

```bash
B) When using distroless images
D) Images doesn’t contain debugging utilities
E) Debugging container in a crashloop

  ```
  </details>


<details>
<summary>
Senario -
Which of the following are incorrect statements? Choose all valid answers

A) The ephemeral container is guaranteed to not eat up the pod’s resources.
B) Ephemeral containers can be restarted after exiting.
C) The --copy-to flag creates a pod that is not owned by the original workload
D) The pid namespace of all containers in a pod is shared with the ephemeral container by default.
</summary>

```bash


A) The ephemeral container is guaranteed to not eat up the pod’s resources.
B) Ephemeral containers can be restarted after exiting.
D) The pid namespace of all containers in a pod is shared with the ephemeral container by default.
  ```
  </details>

  
<details>
<summary>
Senario -
Which exit code indicates the application tried to access a non-existent file?

</summary>

```bash
127

  ```
  </details>

  
<details>
<summary>
Senario -
What cause a CreateContainerError?
</summary>

```bash
A CreateContainerError in Kubernetes typically occurs during the process

Incorrect image specification
Runtime error
Insufficient resources
Missing mounted object

  ```
  </details>

  
<details>
<summary>
Senario -
Which of the following statements are correct?

A. ClusterRoleBindings cannot reference Roles.
B. Creating the RoleBinding in a namespace other than the subjects’ allows the subject to have roles in other namespaces.
C. Adding the namespace under the roleRef in a RoleBinding allows binding subjects to roles in other namespaces.
D. RoleBinding and ServiceAccounts must exist in the same namespace.
E. RoleBindings connect ClusterRoles, but they only give access to the namespace defined in the binding.
</summary>

```bash

A. ClusterRoleBindings cannot reference Roles.
Correct. ClusterRoleBindings are intended to bind ClusterRoles, not Roles. ClusterRoles are designed to grant permissions across the entire cluster or multiple namespaces, whereas Roles are namespace-specific.

B. Creating the RoleBinding in a namespace other than the subjects’ allows the subject to have roles in other namespaces.
Correct. When a RoleBinding is created in a namespace other than where the subjects are, it enables the subjects to have roles in namespaces other than their own.

C. Adding the namespace under the roleRef in a RoleBinding allows binding subjects to roles in other namespaces.
Incorrect. The roleRef in a RoleBinding does not allow for a namespace specification because RoleBindings cannot reference Roles outside their own namespace.

D. RoleBindings and ServiceAccounts must exist in the same namespace.
Incorrect. RoleBindings can be created in any namespace and can reference ServiceAccounts from any namespace.

E. RoleBindings connect ClusterRoles, but they only give access to the namespace defined in the binding.
Correct. RoleBindings connect ClusterRoles to subjects (users or service accounts), but they only grant access within the namespace specified in the binding, ensuring access control boundaries.
  ```
  </details>

 