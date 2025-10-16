## How to install

You can install it with just a few steps.  

### Step 1: Provision AWS with OpenShift Open Environment in RHDP

Access the *AWS with OpenShift Open Environment* catalog.

Provision it with these parameters as below.   
| Param | Value |
| ---  | ---     |
| Region | `us-east-2` |
| Control Plane Instance Type | `m6a.4xlarge` |
| OpenShift Version | `4.19` |


## Step 2: Login to the Bastion Server

After receiving an email titled like `RHDP service AWS with OpenShift Open Environment 559lc is ready`, login to the bastion server with the information in the email.  

```shell
ssh lab-user@bastion.559lc.sandbox185.opentlc.com
```

While processing, you will be asked if you wish to connect and lab-user's password.  
```
The authenticity of host 'bastion.559lc.sandbox185.opentlc.com (3.137.23.91)' can't be established.
ED25519 key fingerprint is SHA256:kMzsfU+LCP1on/YQO+yccng6fMjOT5tW5kaVGXpFx7c.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'bastion.559lc.sandbox185.opentlc.com' (ED25519) to the list of known hosts.
lab-user@bastion.559lc.sandbox185.opentlc.com's password:<lab-user's password given by RHDP> 
```

## Step 3: Clone the Repository  

```shell
git clone https://git:<YOUR_GITLAB_ACCESS_TOKEN>@gitlab.consulting.redhat.com/mmuraki/analysis-agent.git
```

You should see output similar to this:  

```
[lab-user@bastion ~]$ git clone https://git:<YOUR_GITLAB_ACCESS_TOKEN>@gitlab.consulting.redhat.com/mmuraki/analysis-agent.git
Cloning into 'analysis-agent'...
remote: Enumerating objects: 706, done.
remote: Counting objects: 100% (19/19), done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 706 (delta 7), reused 0 (delta 0), pack-reused 687 (from 1)
Receiving objects: 100% (706/706), 691.46 KiB | 8.54 MiB/s, done.
Resolving deltas: 100% (45/45), done.
```

## Step 4: Set your YOUR_GITLAB_ACCESS_TOKEN

```shell
cd analysis-agent/
vim manifest/bootstrap/secret-repo-creds.yaml 
```

You need to replace `YOUR_GITLAB_ACCESS_TOKEN` in `manifest/bootstrap/secret-repo-creds.yaml` with your GitLab access token.  

```
---
apiVersion: v1
kind: Secret
metadata:
  name: repo-creds
  namespace: openshift-gitops
  labels:
    argocd.argoproj.io/secret-type: repo-creds
stringData:
  type: git
  url: https://gitlab.consulting.redhat.com/mmuraki/analysis-agent.git
  password: YOUR_GITLAB_ACCESS_TOKEN
  username: git
```

## Step 5: Run setup.sh

```shell
./setup.sh
```

It takes a little long, almost 30-40 minutes.  
After getting ready, you can see the applicaiton's URL and access it.  

```
[lab-user@bastion analysis-agent]$ ./setup.sh 
clusterrolebinding.rbac.authorization.k8s.io/cluster-admin-openshift-gitops-argocd-application-controller created
namespace/openshift-gitops-operator created
operatorgroup.operators.coreos.com/openshift-gitops-operator created
subscription.operators.coreos.com/openshift-gitops-operator created
Waiting for OpenShift GitOps operator being ready......clusterserviceversion.operators.coreos.com/openshift-gitops-operator.v1.17.1 condition met

secret/repo-creds created
applicationset.argoproj.io/bootstrap created

!!! If you can't get a g6e.xlarge instance, this setup script won't finish. !!!
!!! If it doesn't finish after more than an hour, run 'oc get machine -A'   !!!
!!! to check if the instance is being provisioned.                          !!!

Waiting for the environment being ready. it may take 30-40 minutes...............................................................................................................................................................................................................................
The environment is ready.


[Query application URL]
https://app-query-analysis-agent.apps.cluster-559lc.559lc.sandbox185.opentlc.com/

[Knowledge injection maintenance URL]
https://knowledge-injection-analysis-agent.apps.cluster-559lc.559lc.sandbox185.opentlc.com/
```


