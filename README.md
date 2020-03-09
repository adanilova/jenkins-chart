Kuber: Jenkins CI
=====================
The simpliest deploy to kuber, using Helm and Docker Hub. Create Jenkins CI with pipeline support. No authorization required, no manual configuration needed.

Dependencies
---------------
Helm >= v3.1.1
Jenkins plugins: `workflow-aggregator`, `git`
Kubernetes cluster (based on Docker)

Requirements
---------------
Docker image with pre-install Jenkins and Jenkins plugins
  
Docker Hub
---------------
Check out my example docker image
- [GitHub](https://github.com/adanilova/jenkins-image)
- [DockerHub](https://hub.docker.com/repository/docker/chloeraven/jenkins)

Helm Variables
---------------
Check out my [Helm Chart for Jenkins](https://github.com/adanilova/jenkins-chart)

*General variables*
*path:jenkins-chart/values.yaml*

For deployment:
| Name              | Default Value       |Defenition   |
|-----------------------|---------------------|---------------------|
| `image` | `chloeraven/jenkins:1.0` |Docker image|
| `containerPort` | `8085` |self-explanatory
|`replicaCount` | `1` |self-explanatory
|`runSetupWizard` | `false` |self-explanatory
For service:
| Name              | Default Value       |Defenition   |
|-----------------------|---------------------|---------------------|
|`type` |` LoadBalancer` |An abstract way to expose an application running on a set of Pods as a network service.
|`port` | `8085 `|self-explanatory

For pvc:
| Name              | Default Value       |Defenition   |
|-----------------------|---------------------|---------------------|
|accessMode|`ReadWriteOnce`|self-explanatory|
|storage|`2Gi`|self-explanatory|

Installation
---------------
1) Install all dependencies: [kuberctl](https://kubernetes.io/docs/tasks/tools/install-kubectl/), [helm](https://helm.sh/docs/intro/install/)
2) Set up a Kubernetes cluster. For this task was used GCP
[Install gcloud first](https://cloud.google.com/sdk/docs/quickstart-macos)
```sh
  $  gcloud init
  $  gcloud container clusters create stacy-cluster --zone europe-west4-a
```
3) Clone this repo 
```sh
git clone https://github.com/adanilova/jenkins-chart.git
```

We are ready to start.

Usage
---------------
After git-repo was cloned
```sh
  $ cd ~/jenkins-chart
  $ helm template .
  $ helm apply .
  $ helm install . --generate-name
```

* Execute `kubectl get service` to get Jenkins service External IP address
* Go to Jenkins UI: http://ip_addr:8085/ and run the build for `mytomcat`

Notes
---------------
About init-container in the deployment manifest.
As soon, as Jenkins is a stateful application, I used pvc to store **jenkins_home** directory outside the pod. There was a problem to mount the configmap with my pipeline xml config with **jenkins** user permissions. So I used the init-container to copy pipeline xml config to jenkins_home directory and set right permissions. Right now I didn't find the way to change ownership through "mount volume tools" and didn't enjoy running Jenkins form root user.

Copyright
---------------
This helm chart was created in 2020 by [Anastasia Danilova](https://www.linkedin.com/in/anastasia-danilova-1b7966101/)
