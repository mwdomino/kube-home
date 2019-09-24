## Home Kubernetes cluster on Proxmox VE 6.0

This repo tracks the install of a Kubernetes cluster
on 3 PVE hosts. Each major step is organized into a directory
containing the necessary files to complete it.

There will later contain a `salt-master` to assist with provisioning
the k8s nodes. I'll also be adding `terraform` to automatically
provision the VMs on the cluster with an end goal of immutable
infrastructure on self-hosted baremetal.
