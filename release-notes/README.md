# Nephio R1 Release Notes

## Overview

## Prerequisites

Please refer to the [Install
Guide](https://github.com/nephio-project/docs/blob/main/install-guide/README.md)
for the prerequisites on supported environments.

## Support Matrix

The sandbox environment requires a physical or virtual machine with:
- Ubuntu-20.04-focal Linux
- 8 cores
- 32 GB memory
- 200 GB disk size
- default user with sudo passwordless permissions

This install has been verified on VMs running on Google Cloud, OpenStack, AWS,
vSphere, and Azure. It has been verified on Vagrant VMs running on Windows and
Linux.

For non-sandbox installations, any conforming Kubernetes cluster is sufficient
for the Management cluster.

## Features

### API

CRDs provided for UPF, SMF and AMF 5G Core services

### Web UI

Basic web UI to view and manage the packages and resources within them.

### Packages

* Kpt packages for all [free5gc](https://free5gc.org/) services
* Packages for the core Nephio services
* Packages for the Cluster API services for cluster creation
* Packages for the dependent services

### Functionalities

* Create Kubernetes clusters. This functionality is based on the Cluster API. At
  this time only KIND cluster creation is supported.
* Fully automated deployment of UPF, SMF and AMF services of
  [free5Gc](https://free5gc.org/). These are deployed on multiple clusters
  based on user's intent expressed via the CRDs.
* Deployment of other free5gc functions.
* Auto scaling up of the UPF, SMF and AMF services based on changes to the capacity
  requirements expressed as user intent.

## Limitations

* For infrastructure automation, only creation of KIND clusters is
  supported.
* Inter-cluster networking is not dynamic so as more clusters are
  deployed, some manual tweaking will be needed for inter-cluster communications.
* Provisioning of VLAN interfaces on nodes is manual at this time.
* Feedback of workload deployments from workload clusters to the Management
  cluster is limited. You may need to connect directly to the Workload cluster
  via kubectl to debug any deployment issues.
* Web UI features are limited to view/edit of packages and resources in those
  packages and their deployment. Additional features will be added
  in subsequent releases.
* When the capacities of the UPF,SMF and AMF NFs are changed, the free5gc Operator on the
  Workload cluster will instantiate a new POD with correspondingly modified
  resources (CPU, memory etc.) During this process, the pod will restart. This is a
  limitation of free5gc.
* Only Gitea works with automated cluster provisioning to create new
  repositories and join them to Nephio. To use a different Git provider, you
  must manually provision cluster repositories, register them to the Nephio
  management server, and set up Config Sync on the Workload cluster.
* The WebUI does not require authentication in the current demo configuration.
  Testing of the WebUI with authentication configured has not been done at this
  time.
* The WebUI only shows resources in the default namespace.
* While many types of Git authentication are supported, the testing was only
  done with token-based Git authentication in Gitea.

## Known Issues and Workarounds

* When deploying the sandbox environment on an Ubuntu VM running on OpenStack,
  the deployment may fail. Reinstalling the packages will get around this issue.
* Occasionally packages may take a long time to be approved by the auto-approval
  controller. If they seem stuck, restarting Porch and the Nephio controllers
  may help:
  ```bash
  kubectl -n porch-system rollout restart deploy porch-server
  kubectl -n nephio-system rollout restart deploy nephio-controller
  ```
* Occasional calls to `kpt alpha rpkg copy` may fail with a message like
  `Error: Internal error occurred: error applying patch: conflict: fragment line
  does not match src line`. Try again in a little while, this may clear up on
  its own. Restarting Porch may also help.
