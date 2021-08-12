# 6-app-workload-examples

This repo is part of a multi-part guide that shows how to configure and deploy
the example.com reference architecture described in
[Google Cloud security foundations guide](https://services.google.com/fh/files/misc/google-cloud-security-foundations-guide.pdf)
(PDF). The following table lists the parts of the guide.

For an overview of the architecture and the parts, see the
[terraform-example-foundation README](https://github.com/terraform-google-modules/terraform-example-foundation)
file.

## Purpose

The purpose of this step is to showcase several use cases and example workload deployments in one of the business unit projects using the infra pipeline set up in `4-projects`.

The infra pipeline is created in step `4-projects` within the shared env and has a [Cloud Build](https://cloud.google.com/build/docs) pipeline configured to manage infrastructure within projects.
To enable deployment via this pipeline, the projects deployed should [enable](https://github.com/terraform-google-modules/terraform-example-foundation/blob/master/4-projects/business_unit_1/development/example_base_shared_vpc_project.tf#L31-L32) the `enable_cloudbuild_deploy` flag and provide the Cloud Build service account value via `cloudbuild_sa`.

This enables the Cloud Build service account to impersonate the project service account and use it to deploy infrastructure. The roles required for the project SA can also be [managed](https://github.com/terraform-google-modules/terraform-example-foundation/blob/master/4-projects/business_unit_1/development/example_base_shared_vpc_project.tf#L30) via `sa_roles`. (Note: This requires per project SA impersonation. If you would like to have a single SA managing an environment and all associated projects, that is also possible by [granting](https://github.com/terraform-google-modules/terraform-example-foundation/blob/master/4-projects/modules/single_project/main.tf#L62-L68) `roles/iam.serviceAccountTokenCreator` to an SA with the right roles in `4-projects/env`.)

There is also a [Source Repository](https://cloud.google.com/source-repositories) configured with build triggers similar to the [foundation pipeline](https://github.com/terraform-google-modules/terraform-example-foundation#0-bootstrap) setup in `0-bootstrap`.
This Compute Engine instance is created using the base network from step `3-networks` and is used to access private services.

### 💬 Example Workloads

We created the following example workloads:

- [Managed Instance Groups](https://cloud.google.com/compute/docs/instance-groups) with [External HTTP Load Balancer](https://cloud.google.com/load-balancing/docs/https) to expose private instances, and [Cloud Armor](https://cloud.google.com/armor) XSS and SQL injection policies enabled
- [GKE Safer Cluster](https://registry.terraform.io/modules/terraform-google-modules/kubernetes-engine/google/latest/submodules/safer-cluster) for a safer-than-default GKE configuration
- [Cloud Pub/Sub](https://cloud.google.com/pubsub) and [Cloud Asset Inventory](https://cloud.google.com/asset-inventory) feeds to detect creation of service accounts for further investigation or remediative actions