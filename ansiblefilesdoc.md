# Ansible FILES modules
### *Local copy of files modules*

---
### Requirements
* See official Ansible docs

---
### Modules

  * [oc_service - create, modify, and idempotently manage openshift services.](#oc_service)
  * [oc_obj - generic interface to openshift objects](#oc_obj)
  * [oc_secret - module to manage openshift secrets](#oc_secret)
  * [oc_process - module to process openshift templates](#oc_process)
  * [oadm_manage_node - module to manage openshift nodes](#oadm_manage_node)
  * [oc_adm_ca_server_cert - module to run openshift oc adm ca create-server-cert](#oc_adm_ca_server_cert)
  * [oc_serviceaccount - module to manage openshift service accounts](#oc_serviceaccount)
  * [oc_serviceaccount_secret - module to manage openshift service account secrets](#oc_serviceaccount_secret)
  * [oc_version - return the current openshift version](#oc_version)
  * [oc_scale - manage openshift services through the scale parameters](#oc_scale)
  * [oc_route - create, modify, and idempotently manage openshift routes.](#oc_route)
  * [oc_adm_registry - module to manage openshift registry](#oc_adm_registry)
  * [oc_edit - modify, and idempotently manage openshift objects.](#oc_edit)

---

## oc_service
Create, modify, and idempotently manage openshift services.

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Manage openshift service objects programmatically.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| name  |   no  |    | |  Name of the object that is being queried.  |
| debug  |   no  |  False  | |  Turn on debug output.  |
| labels  |   no  |    | |  The labels to apply on the service.  |
| namespace  |   no  |  default  | |  The namespace where the object lives.  |
| selector  |   no  |    | |  The selector to apply when filtering for services.  |
| clusterip  |   no  |    | |  The cluster ip address to use with this service.  |
| state  |   no  |  present  | <ul> <li>present</li>  <li>absent</li>  <li>list</li> </ul> |  State represents whether to create, modify, delete, or list  |
| portalip  |   no  |    | |  The portal ip(virtual ip) address to use with this service.  https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/pods_and_services.html#services  |
| service_type  |   no  |  ClusterIP  | <ul> <li>ClusterIP</li>  <li>NodePort</li>  <li>LoadBalancer</li>  <li>ExternalName</li> </ul> |  The type of service desired.  Each option tells the service to behave accordingly.  https://kubernetes.io/docs/user-guide/services/  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| ports  |   no  |    | |  A list of the ports that are used for this service.  This includes name, port, protocol, and targetPort.  See examples.  |
| session_affinity  |   no  |    | |  The type of session affinity to use.  |


 
#### Examples

```
- name: get docker-registry service
  run_once: true
  oc_service:
    namespace: default
    name: docker-registry
    state: list
  register: registry_service_out

- name: create the docker-registry service
  oc_service:
    namespace: default
    name: docker-registry
    ports:
    - name: 5000-tcp
      port: 5000
      protocol: TCP
      targetPort: 5000
    selector:
      docker-registry: default
    session_affinity: ClientIP
    service_type: ClusterIP
  register: svc_out
  notify:
  - restart openshift master services

```



---


## oc_obj
Generic interface to openshift objects

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Manage openshift objects programmatically.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| files  |   no  |    | |  A list of files provided for object  |
| kind  |   yes  |    | |  The kind attribute of the object. e.g. dc, bc, svc, route  |
| all_namespace  |   no  |  False  | |  The namespace where the object lives.  |
| name  |   no  |    | |  Name of the object that is being queried.  |
| namespace  |   no  |  str  | |  The namespace where the object lives.  |
| delete_after  |   no  |  False  | |  Whether or not to delete the files after processing them.  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| content  |   no  |    | |  Content of the object being managed.  |
| state  |   yes  |  present  | <ul> <li>present</li>  <li>absent</li>  <li>list</li> </ul> |  Currently present is only supported state.  |
| debug  |   no  |  False  | |  Turn on debug output.  |
| force  |   no  |    | |  Whether or not to force the operation  |
| selector  |   no  |    | |  Selector that gets added to the query.  |


 
#### Examples

```
oc_obj:
  kind: dc
  name: router
  namespace: default
register: router_output

```



---


## oc_secret
Module to manage openshift secrets

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Manage openshift secrets programmatically.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| files  |   no  |    | |  A list of files provided for secrets  |
| force  |   no  |  False  | |  Whether or not to force the operation  |
| name  |   no  |    | |  Name of the object that is being queried.  |
| namespace  |   no  |  default  | |  The namespace where the object lives.  |
| delete_after  |   no  |  False  | |  Whether or not to delete the files after processing them.  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| decode  |   no  |  False  | |  base64 decode the object  |
| state  |   no  |  present  | <ul> <li>present</li>  <li>absent</li>  <li>list</li> </ul> |  If present, the secret will be created if it doesn't exist or updated if different. If absent, the secret will be removed if present. If list, information about the secret will be gathered and returned as part of the Ansible call results.  |
| debug  |   no  |  False  | |  Turn on debug output.  |
| contents  |   no  |    | |  Content of the secrets  |


 
#### Examples

```
- name: create secret
  oc_secret:
    state: present
    namespace: openshift-infra
    name: metrics-deployer
    files:
    - name: nothing
      path: /dev/null
  register: secretout
  run_once: true

- name: get ca from hawkular
  oc_secret:
    state: list
    namespace: openshift-infra
    name:  hawkular-metrics-certificate
    decode: True
  register: hawkout
  run_once: true

- name: Create secrets
  oc_secret:
    namespace: mynamespace
    name: mysecrets
    contents:
    - path: data.yml
      data: "{{ data_content }}"
    - path: auth-keys
      data: "{{ auth_keys_content }}"
    - path: configdata.yml
      data: "{{ configdata_content }}"
    - path: cert.crt
      data: "{{ cert_content }}"
    - path: key.pem
      data: "{{ osso_site_key_content }}"
    - path: ca.cert.pem
      data: "{{ ca_cert_content }}"
  register: secretout

```



---


## oc_process
Module to process openshift templates

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Process openshift templates programmatically.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| reconcile  |   |  True  | |  Whether or not to attempt to determine if there are updates or changes in the incoming template.  |
| template_name  |   no  |    | |  Name of the openshift template that is being processed.  |
| create  |   no  |  False  | |  Whether or not to create the template after being processed. e.g.  oc process | oc create -f -  |
| namespace  |   no  |  default  | |  The namespace where the template lives.  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| content  |   no  |    | |  Template content that will be processed.  |
| state  |   |  present  | <ul> <li>present</li>  <li>absent</li>  <li>list</li> </ul> |  State has a few different meanings when it comes to process.  {u'state': u'present - This state runs an `oc process <template>`.  When used in'}  {u"conjunction with 'create": u"True' the process will be piped to | oc create -f"}  {u'state': u'absent - will remove a template'}  {u'state': u'list - will perform an `oc get template <template_name>`'}  |
| params  |   no  |    | |  A list of parameters that will be inserted into the template.  |
| debug  |   no  |  False  | |  Turn on debug output.  |


 
#### Examples

```
- name: process the cloud volume provisioner template with variables
  oc_process:
    namespace: openshift-infra
    template_name: online-volume-provisioner
    create: True
    params:
      PLAT: rhel7
  register: processout
  run_once: true
- debug: var=processout

```



---


## oadm_manage_node
Module to manage openshift nodes

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Manage openshift nodes programmatically.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| node  |   no  |    | |  A list of the nodes being managed  |
| schedulable  |   no  |    | |  whether or not openshift can schedule pods on this node  |
| force  |   no  |    | |  Whether or not to attempt to force this action in openshift  |
| dry_run  |   no  |  False  | |  This shows the pods that would be migrated if evacuate were called  |
| grace_period  |   no  |    | |  Grace period (seconds) for pods being deleted.  |
| evacuate  |   no  |  False  | |  Remove all pods from a node.  |
| selector  |   no  |    | |  The selector when filtering on node labels  |
| debug  |   no  |  False  | |  Turn on debug output.  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| pod_selector  |   no  |    | |  A selector when filtering on pod labels.  |


 
#### Examples

```
- name: oadm manage-node --schedulable=true --selector=ops_node=new
  oadm_manage_node:
    selector: ops_node=new
    schedulable: True
  register: schedout

- name: oadm manage-node my-k8s-node-5 --evacuate
  oadm_manage_node:
    node:  my-k8s-node-5
    evacuate: True
    force: True

```



---


## oc_adm_ca_server_cert
Module to run openshift oc adm ca create-server-cert

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Wrapper around the openshift `oc adm ca create-server-cert` command.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| force  |   no  |  False  | |  Force updating of the existing cert and key files  |
| signer_key  |   no  |  /etc/origin/master/ca.key  | |  The signer key file.  |
| signer_serial  |   no  |  /etc/origin/master/ca.serial.txt  | |  The signer serial file.  |
| signer_cert  |   no  |  /etc/origin/master/ca.crt  | |  The signer certificate file.  |
| state  |   no  |  present  | <ul> <li>present</li> </ul> |  Present is the only supported state.  The state present means that `oc adm ca` will generate a certificate  and verify if the hostnames and the ClusterIP exists in the certificate.  When create-server-cert is desired then the following parameters are passed.  [u'cert', u'key', u'signer_cert', u'signer_key', u'signer_serial']  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| cert  |   no  |    | |  The certificate file. Choose a name that indicates what the service is.  |
| hostnames  |   no  |  []  | |  Every hostname or IP that server certs should be valid for  |
| key  |   no  |    | |  The key file. Choose a name that indicates what the service is.  |
| debug  |   no  |  False  | |  Turn on debug output.  |
| backup  |   no  |  True  | |  Whether to backup the cert and key files before writing them.  |


 
#### Examples

```
- name: Create a self-signed cert
  oc_adm_ca_server_cert:
    signer_cert: /etc/origin/master/ca.crt
    signer_key: /etc/origin/master/ca.key
    signer_serial: /etc/origin/master/ca.serial.txt
    hostnames: "registry.test.openshift.com,127.0.0.1,docker-registry.default.svc.cluster.local"
    cert: /etc/origin/master/registry.crt
    key: /etc/origin/master/registry.key

```



---


## oc_serviceaccount
Module to manage openshift service accounts

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Manage openshift service accounts programmatically.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| name  |   yes  |    | |  Name of the service account.  |
| image_pull_secrets  |   no  |    | |  A list of the image pull secrets that are associated with the service account.  |
| secrets  |   no  |    | |  A list of secrets that are associated with the service account.  |
| namespace  |   yes  |  default  | |  Namespace of the service account.  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| state  |   no  |  present  | <ul> <li>present</li>  <li>absent</li>  <li>list</li> </ul> |  If present, the service account will be created if it doesn't exist or updated if different. If absent, the service account will be removed if present. If list, information about the service account will be gathered and returned as part of the Ansible call results.  |
| debug  |   no  |  False  | |  Turn on debug output.  |


 
#### Examples

```
- name: create registry serviceaccount
  oc_serviceaccount:
    name: registry
    namespace: default
    secrets:
    - docker-registry-config
    - registry-secret
  register: sa_out

```



---


## oc_serviceaccount_secret
Module to manage openshift service account secrets

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Manage openshift service account secrets programmatically.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| namespace  |   yes  |    | |  Namespace of the service account and secret.  |
| secret  |   no  |    | |  The secret that should be linked to the service account.  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| state  |   no  |  present  | <ul> <li>present</li>  <li>absent</li>  <li>list</li> </ul> |  If present, the service account will be linked with the secret if it is not already. If absent, the service account will be unlinked from the secret if it is already linked. If list, information about the service account secrets will be gathered and returned as part of the Ansible call results.  |
| service_account  |   yes  |    | |  Name of the service account.  |
| debug  |   no  |  False  | |  Turn on debug output.  |


 
#### Examples

```
  - name: get secrets of a service account
    oc_serviceaccount_secret:
      state: list
      service_account: builder
      namespace: default
    register: sasecretout


  - name: Link a service account to a specific secret
    oc_serviceaccount_secret:
      service_account: builder
      secret: mynewsecret
      namespace: default
    register: sasecretout

```



---


## oc_version
Return the current openshift version

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Return the openshift installed version.  `oc version`

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| debug  |   no  |  False  | |  Turn on debug output.  |
| state  |   yes  |  list  | <ul> <li>list</li> </ul> |  Currently list is only supported state.  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |


 
#### Examples

```
oc_version:
- name: get oc version
  oc_version:
  register: oc_version

```



---


## oc_scale
Manage openshift services through the scale parameters

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Manage openshift services through scaling them.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| kind  |   no  |    | <ul> <li>rc</li>  <li>dc</li> </ul> |  The kind of object to scale.  |
| name  |   no  |    | |  Name of the object that is being queried.  |
| namespace  |   no  |  default  | |  The namespace where the object lives.  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| state  |   yes  |  present  | <ul> <li>present</li>  <li>list</li> </ul> |  State represents whether to scale or list the current replicas  |
| debug  |   no  |  False  | |  Turn on debug output.  |


 
#### Examples

```
- name: scale down a rc to 0
  oc_scale:
    name: my-replication-controller
    kind: rc
    namespace: openshift-infra
    replicas: 0

- name: scale up a deploymentconfig to 2
  oc_scale:
    name: php
    kind: dc
    namespace: my-php-app
    replicas: 2

```



---


## oc_route
Create, modify, and idempotently manage openshift routes.

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Manage openshift route objects programmatically.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| cacert_path  |   no  |    | |  The path to the cacert  |
| name  |   no  |    | |  Name of the object that is being queried.  |
| dest_cacert_content  |   no  |    | |  The dest_cacert content  |
| service_name  |   no  |    | |  The name of the service that this route points to.  |
| namespace  |   no  |  str  | |  The namespace where the object lives.  |
| host  |   no  |    | |  The host that the route will use. e.g. myapp.x.y.z  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| dest_cacert_path  |   no  |    | |  The path to the dest_cacert  |
| state  |   yes  |  present  | <ul> <li>present</li>  <li>absent</li>  <li>list</li> </ul> |  State represents whether to create, modify, delete, or list  |
| key_path  |   no  |    | |  The path to the key  |
| cacert_content  |   no  |    | |  The cacert content  |
| debug  |   no  |  False  | |  Turn on debug output.  |
| cert_path  |   no  |    | |  The path to the cert  |
| tls_termination  |   no  |    | |  The options for termination. e.g. reencrypt  |
| cert_content  |   no  |    | |  The cert content  |
| port  |   no  |    | |  The Name of the service port or number of the container port the route will route traffic to  |


 
#### Examples

```
- name: Configure certificates for reencrypt route
  oc_route:
    name: myapproute
    namespace: awesomeapp
    cert_path: "/etc/origin/master/named_certificates/myapp_cert
    key_path: "/etc/origin/master/named_certificates/myapp_key
    cacert_path: "/etc/origin/master/named_certificates/myapp_cacert
    dest_cacert_content:  "{{ dest_cacert_content }}"
    service_name: myapp_php
    host: myapp.awesomeapp.openshift.com
    tls_termination: reencrypt
  run_once: true

```



---


## oc_adm_registry
Module to manage openshift registry

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Manage openshift registry programmatically.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| tls_key  |   no  |    | |  An optional path to a PEM encoded private key for serving over TLS  |
| name  |   no  |    | |  The name of the registry  |
| replicas  |   no  |  1  | |  The replication factor of the registry; commonly 2 when high availability is desired.  |
| env_vars  |   no  |    | |  {u'A dictionary of modifications to make on the deploymentconfig. e.g. FOO': u'BAR'}  |
| labels  |   no  |    | |  A set of labels to uniquely identify the registry and its components.  |
| namespace  |   no  |    | |  The selector when filtering on node labels  |
| state  |   no  |  False  | |  The desired action when managing openshift registry  present - update or create the registry  absent - tear down the registry service and deploymentconfig  list - returns the current representiation of a registry  |
| enforce_quota  |   no  |  False  | |  If set, the registry will refuse to write blobs if they exceed quota limits  |
| selector  |   no  |    | |  Selector used to filter nodes on deployment. Used to run registries on a specific set of nodes.  |
| tls_certificate  |   no  |    | |  An optional path to a PEM encoded certificate (which may contain the private key) for serving over TLS  |
| images  |   openshift3/ose-${component}:${version}  |    | |  The image to base this registry on - ${component} will be replaced with --type  |
| edits  |   no  |    | |  A list of modifications to make on the deploymentconfig  |
| volume_mounts  |   no  |    | |  The volume mounts for the registry.  |
| service_account  |   no  |  registry  | |  Name of the service account to use to run the registry pod.  |
| latest_images  |   no  |  False  | |  If true, attempt to use the latest image for the registry instead of the latest release.  |
| debug  |   no  |  False  | |  Turn on debug output.  |
| force  |   no  |  False  | |  Force a registry update.  |
| mount_host  |   no  |  False  | |  If set, the registry volume will be created as a host-mount at this path.  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| ports  |   no  |  [5000]  | |  A comma delimited list of ports or port pairs to expose on the registry pod.  The default is set for 5000.  |
| daemonset  |   no  |  False  | |  Use a daemonset instead of a deployment config.  |


 
#### Examples

```
- name: create a secure registry
  oc_adm_registry:
    name: docker-registry
    service_account: registry
    replicas: 2
    namespace: default
    selector: type=infra
    images: "registry.ops.openshift.com/openshift3/ose-${component}:${version}"
    env_vars:
      REGISTRY_CONFIGURATION_PATH: /etc/registryconfig/config.yml
      REGISTRY_HTTP_TLS_CERTIFICATE: /etc/secrets/registry.crt
      REGISTRY_HTTP_TLS_KEY: /etc/secrets/registry.key
      REGISTRY_HTTP_SECRET: supersecret
    volume_mounts:
    - path: /etc/secrets
      name: dockercerts
      type: secret
      secret_name: registry-secret
    - path: /etc/registryconfig
      name: dockersecrets
      type: secret
      secret_name: docker-registry-config
    edits:
    - key: spec.template.spec.containers[0].livenessProbe.httpGet.scheme
      value: HTTPS
      action: put
    - key: spec.template.spec.containers[0].readinessProbe.httpGet.scheme
      value: HTTPS
      action: put
    - key: spec.strategy.rollingParams
      value:
        intervalSeconds: 1
        maxSurge: 50%
        maxUnavailable: 50%
        timeoutSeconds: 600
        updatePeriodSeconds: 1
      action: put
    - key: spec.template.spec.containers[0].resources.limits.memory
      value: 2G
      action: update
    - key: spec.template.spec.containers[0].resources.requests.memory
      value: 1G
      action: update

  register: registryout


```



---


## oc_edit
Modify, and idempotently manage openshift objects.

  * Synopsis
  * Options
  * Examples

#### Synopsis
 Modify openshift objects programmatically.

#### Options

| Parameter     | required    | default  | choices    | comments |
| ------------- |-------------| ---------|----------- |--------- |
| kind  |   yes  |    | <ul> <li>bc</li>  <li>buildconfig</li>  <li>configmaps</li>  <li>dc</li>  <li>deploymentconfig</li>  <li>imagestream</li>  <li>imagestreamtag</li>  <li>is</li>  <li>istag</li>  <li>namespace</li>  <li>project</li>  <li>projects</li>  <li>node</li>  <li>ns</li>  <li>persistentvolume</li>  <li>pv</li>  <li>rc</li>  <li>replicationcontroller</li>  <li>routes</li>  <li>scc</li>  <li>secret</li>  <li>securitycontextconstraints</li>  <li>service</li>  <li>svc</li> </ul> |  The kind attribute of the object.  |
| force  |   no  |    | |  Whether or not to force the operation  |
| name  |   no  |    | |  Name of the object that is being queried.  |
| file_format  |   no  |  yaml  | |  The format of the file being edited.  |
| file_name  |   no  |    | |  The file name in which to edit  |
| namespace  |   no  |  str  | |  The namespace where the object lives.  |
| kubeconfig  |   no  |  /etc/origin/master/admin.kubeconfig  | |  The path for the kubeconfig file to use for authentication  |
| content  |   no  |    | |  Content of the file  |
| state  |   yes  |  present  | <ul> <li>present</li> </ul> |  Currently present is only supported state.  |
| separator  |   no  |  .  | |  The separator format for the edit.  |
| debug  |   no  |  False  | |  Turn on debug output.  |


 
#### Examples

```
oc_edit:
  kind: rc
  name: hawkular-cassandra-rc
  namespace: openshift-infra
  content:
    spec.template.spec.containers[0].resources.limits.memory: 512
    spec.template.spec.containers[0].resources.requests.memory: 256

```



---


---
Created by Network to Code, LLC
For:
2015
