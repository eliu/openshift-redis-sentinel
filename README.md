# Redis Sentinel Deployment Template for OpenShift (Origin)

## Overview

This project aims to provide a missing template for creating Redis Sentinel cluster in OpenShift Origin. The basic idea is to create a standalone **pod** with 1 replica to launch a redis master along with a sentinel container in it. Then launch a DeploymentConfig (dc) to create and scale multiple redis slaves. This dc contains a pod with a redis slave container and redis sentinel container. 

## Prerequisites
Before using the template including in this project, you need to make sure the following prerequisites:

1. OpenShift Origin server v3.6.0 or above available
2. OpenShift cli tool **oc** must be installed in your host machine
3. A private registry or using Docker Hub to maintain your redis sentinel image

*Note: you can get OpenShift via installing MiniShift or download OpenShift Origin server from Github into your VM (RHEL or CentOS recommended)*

- [Install Minishift (Recommended)](https://docs.openshift.org/latest/minishift/getting-started/installing.html)
- [OpenShift Advanced Install](https://docs.openshift.org/latest/install_config/install/advanced_install.html)

## Import Template
The OpenShift templates and image streams are often been created and maintained within **openshift** project, which is one of internal projects by OpenShift itself. To import your template, you need to login as **system:admin** to do that. The sample script would be something as follows:
```bash
$ cd openshift-redis-sentinel
$ oc login -u system:admin -n openshift
$ oc create -f templates/redis-sentinel-template.json
```

## Deploy Your App using Template

1. Deploy Redis Sentinel app into your project

```bash
$ oc login -u <username> -p <password> -n <your_project>
$ oc process redis-sentinel | oc create -f -
```

2. Get ingress port and test your redis sentinel

```bash
# Get redis ingress port
$ oc export svc redis-sentinel-ingress | grep 'nodePort'
#   - nodePort: 31443
# Take minishift as instance
$ redis-cli -h $(minishift ip) -p 31443
```

## Important Notice

When deployment is completed, the Redis Sentinel cluster is actually not fully up and running. You need to wait **for a short while** and attempt to login redis via cli by the ingress port exposed by service **redis-sentinel-ingress**.

## References

This project is inspired by the following repo or resources. Many thanks to the respected authors.

- [Running Redis Cluster on OpenShift 3.1](https://github.com/shah-zobair/redis-sentinel#running-redis-cluster-on-openshift-31)
- [Kubernetes Examples: Reliable, Scalable Redis on Kubernetes](https://github.com/kubernetes/examples/tree/master/staging/storage/redis)
- [OpenShift Libraries: Redis Template](https://github.com/openshift/library/tree/master/community/redis)
- [Openshift: how to edit scc non-interactively?](https://stackoverflow.com/questions/42310262/openshift-how-to-edit-scc-non-interactively)
- [OpenShift Templates](https://docs.openshift.com/container-platform/3.4/dev_guide/templates.html)
- [Deploy Redis Sentinel Cluster With K8s](https://o-my-chenjian.com/2017/02/06/Deploy-Redis-Sentinel-Cluster-With-K8s/)