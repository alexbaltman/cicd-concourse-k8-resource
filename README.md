# Kubernetes Resource
# based on jcderr/concourse-kubernetes-resource

## Installing

```
resource_types:
- name: kubernetes
  type: docker-image
  source:
    repository: cisco-cis/cicd-concourse-k8-resource
resources:
- name: kubernetes
  type: kubernetes
  source:
    cluster_url: https://hostname:port
    namespace: default
    cluster_ca: _base64 encoded CA pem_
    admin_key: _base64 encoded key pem_
    admin_cert: _base64 encoded certificate_
    resource_type: deployment
    resource_name: some_pod_name
    container_name: some_container
```

## Source Configuration

* `cluster_url`: *Required.* URL to Kubernetes Master API service
* `namespace`: *Required.* Kubernetes namespace.
* `cluster_ca`: *Optional.* Base64 encoded PEM. Required if `cluster_url` is https.
* `admin_key`: *Optional.* Base64 encoded PEM. Required if `cluster_url` is https.
* `admin_cert`: *Optional.* Base64 encoded PEM. Required if `cluster_url` is https.
* `resource_type`: *Required.* Resource type to operate upon (valid values: `deployment`, `replicationcontroller`, `job`).
* `resource_type`: *Required.*  `job - create a new k8 deployment with a spec file pointed with resource_path`).
* `resource_type`: *Required.*  `deployment - update an already existing k8 deployment with deployment`, `replicationcontroller`, `job`).
* `resource_type`: *Required.*  `replicationcontroller - update an already existing k8 deployment with replicationcontroller`).
* `resource_name`: *Required.* Resource name to operate upon.
* `container_name`: *Optional.* For multi-container pods, specify the name of the container being updated. (Default: resource_name)

#### `out`: Begins Kubernetes Deploy Process

Applies a kubectl action.

#### Parameters
* `image_name`: *Required.* Path to file containing docker image name.
* `image_tag`: *Required.* Path to file container docker image tag.
* `resource_path`: *Required.* Resource path for k8 spec file for new k8 deployment.

## Example

### Out
```
---
resources:
- name: k8s
  type: kubernetes
  source:
    cluster_url: https://kube-master.domain.example
    namespace: alpha
    resource_type: deployment
    resource_name: myapp
    container_name: mycontainer
    cluster_ca: _base64 encoded CA pem_
    admin_key: _base64 encoded key pem_
    admin_cert: _base64 encoded certificate pem_
```

```
---
- put: k8s
  params:
    image_name: docker/repository
    image_tag: docker/tag
```

```
resource_types:
- name: kubernetes
  type: docker-image
  source:
    repository: whewawal/cc-k8-resource
```

```
- name: k8s
  type: kubernetes
  source:
    cluster_url: http://10.203.188.248:8080
    namespace: default
    resource_type: job
    resource_name: aci-app
    container_name: aci
```

```
- name: deploy-app
  public: true
  serial: true
  plan:
    - get: github-code
      trigger: true
      passed: [unit-test]
    - get: docker-registry
      trigger: true
    - put: k8s
      params:
        image_name: docker-registry/repository
        image_tag: docker-registry/tag
        resource_path: github-code/spec.yml
```
