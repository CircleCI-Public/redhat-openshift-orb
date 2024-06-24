# Red Hat OpenShift Orb 

This orb is no longer supported by CircleCI because of limited or no use. If you would like to use this orb or create your own version feel free to fork the repository and use the following https://circleci.com/blog/building-private-orbs/ as a guide to making this orb into a private orb for your own use. 

[![CircleCI status](https://circleci.com/gh/CircleCI-Public/redhat-openshift-orb.svg "CircleCI status")](https://circleci.com/gh/CircleCI-Public/redhat-openshift-orb) [![CircleCI Orb Version](https://img.shields.io/badge/endpoint.svg?url=https://badges.circleci.io/orb/circleci/redhat-openshift)](https://circleci.com/orbs/registry/orb/circleci/redhat-openshift) [![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/circleci-public/redhat-openshift-orb/master/LICENSE) [![CircleCI Community](https://img.shields.io/badge/community-CircleCI%20Discuss-343434.svg)](https://discuss.circleci.com/c/ecosystem/orbs)

A CircleCI Orb to simplify deployments to Red Hat OpenShift.

Here are some features that the Red Hat OpenShift orb provides:

- Allowing `kubectl` and other tools that access `kubeconfig` (like `helm`) to connect to a local/remote Red Hat OpenShift cluster via the `login-and-update-kubeconfig` command
- Creating of a local Red Hat OpenShift cluster via the `create-local-cluster-with-oc` command. This could be useful for tests.
- Installing the Red Hat OpenShift client CLI (`oc`) via the `install-openshift-cli` command.

## Usage

See the [orb registry listing](http://circleci.com/orbs/registry/orb/circleci/redhat-openshift) for usage guidelines.

## Requirements

- `curl` should be present in `PATH`.

## Examples

Full usage examples can be found on the Red Hat OpenShift orb's page in the orb registry, [here](https://circleci.com/orbs/registry/orb/circleci/redhat-openshift#usage-examples).

```
version: 2.1

orbs:
  redhat-openshift: circleci/redhat-openshift@0.1.0
  kubernetes: circleci/kubernetes@0.3.0

jobs:
  deploy-to-cluster:
    executor: redhat-openshift/default
    steps:
      - redhat-openshift/login-and-update-kubeconfig:
          # e.g. https://api.mycluster.myserver.org:6443
          server-address: $OPENSHIFT_SERVER
          username: $OPENSHIFT_USER
          password: $OPENSHIFT_PASSWORD
          # Enable this if the cluster uses a self-signed cert
          insecure-skip-tls-verify: true
          openshift-platform-version: "4.x"
      - run:
          name: Create example k8s deployment yaml file
          command: |
            cat \<<- EOF > deployment.yaml
            apiVersion: apps/v1
            kind: Deployment
            metadata:
              name: nginx-deployment
            spec:
              selector:
                matchLabels:
                  app: nginx
              replicas: 2
              template:
                metadata:
                  labels:
                    app: nginx
                spec:
                  containers:
                  - name: nginx
                    image: nginx:1.7.9
                    ports:
                    - containerPort: 80
            EOF
      - kubernetes/create-or-update-resource:
          resource-file-path: "deployment.yaml"
          resource-name: "deployment/nginx-deployment"
          get-rollout-status: true
workflows:
  deployment:
    jobs:
      - deploy-to-cluster
```

## Contributing

We welcome [issues](https://github.com/CircleCI-Public/redhat-openshift-orb/issues) to and [pull requests](https://github.com/CircleCI-Public/redhat-openshift-orb/pulls) against this repository!

For further questions/comments about this or other orbs, visit [CircleCI's orbs discussion forum](https://discuss.circleci.com/c/orbs).
