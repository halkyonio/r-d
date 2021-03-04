## Install cf-for-k8s (aka VMWare Tanzu Application Service) 

Additional information about how to install/configure is available with the project [instructions](https://github.com/cloudfoundry/cf-for-k8s/blob/master/docs/deploy.md)

- Git clone the project
```bash
git clone https://github.com/cloudfoundry/cf-for-k8s.git && cd cf-for-k8s
```
- Generate the `install` values such as domain name, app domain, certificates, ... using the bosh client
```bash
IP=95.217.159.244
./hack/generate-values.sh -d ${IP}.nip.io > /tmp/cf-values.yml
```
- Pass your credentials to access the container registry (quay.io, docker, or local)
```bash
cat << EOF >> /tmp/cf-values.yml
app_registry:
  hostname: https://quay.io/
  repository_prefix: quay.io/cmoulliard
  username: "cmoulliard"
  password: "xxxxx"

add_metrics_server_components: true
enable_automount_service_account_token: true
load_balancer:
  enable: false
metrics_server_prefer_internal_kubelet_address: true
remove_resource_requirements: true
use_first_party_jwt_tokens: true
EOF
```  

- Use the `Spring Boot` builder which don t include the `Autoreconfiguration` pack as it will fail with applications using the Pivotal `CfEnv` project
```bash
echo -e '\nimages:\n  cf_autodetect_builder: cmoulliard/paketo-spring-boot-builder@sha256:f0fe222b06fd54e580a1366646f31e7b5b59047c3112b8416c06994e4109cd30' >> /tmp/cf-values.yml
```
- Deploy the Kubernetes metrics server needed by the `metrics-proxy` pod
```bash
kc apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
```
- Next, deploy `cf-4-k8s` using the `kapp` tool
```bash
kapp deploy -a cf -f <(ytt -f config -f /tmp/cf-values.yml)
```
- **REMARK**: When using `kind`, please execute the following command to use the `nodeport-for-ingress` as kind don't provide a loadbalancer that `istio ingressgateway` can use and fix resource allocations.
```bash
ytt -f config -f config-optional/remove-resource-requirements.yml -f config-optional/remove-ingressgateway-service.yml -f config-optional/use-nodeport-for-ingress.yml -f /tmp/cf-values.yml > /tmp/cf-for-k8s-rendered.yml
kapp deploy -a cf -f /tmp/cf-for-k8s-rendered.yml -y
```
- **REMARK**: When the `ingress nginx controller` has been deployed on kubernetes created using by example `kubeadm, kubelet`, then scale it down the `ingress nginx`, otherwise cf for k8s will fail to be deployed !!
```bash
$ kc scale --replicas=0 deployment.apps/nginx-ingress-controller -n kube-system
```

- **REMARK**: When using `kind`, please execute the following command to remove istio ingress service and fix health check, cpu/memory
```bash
kapp deploy -a cf -f <(ytt -f config -f /tmp/cf-values.yml -f config/remove-resource-requirements.yml -f config/istio/ingressgateway-service-nodeport.yml)
```