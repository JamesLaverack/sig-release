# v1.18.0-rc.1

## Changelog since v1.17.0-rc.1

## Urgent Upgrade Notes

### (No, really, you MUST read this before you upgrade)

- Action required
  1. Currently, if users were to explicitly specify CacheSize of 0 for KMS provider, they would end-up with a provider that caches up to 1000 keys. This PR changes this behavior.
  Post this PR, when users supply 0 for CacheSize this will result in a validation error.
  
  2. CacheSize type was changed from int32 to *int32. This allows defaulting logic to differentiate between cases where users explicitly supplied 0 vs. not supplied any value.
  3. KMS Provider's endpoint (path to Unix socket) is now validated when the EncryptionConfiguration files is loaded. This used to be handled by the GRPCService. ([#85363](https://github.com/kubernetes/kubernetes/pull/85363), [@immutableT](https://github.com/immutableT)) [SIG API Machinery, Auth and Instrumentation]

- The StreamingProxyRedirects feature and `--redirect-container-streaming` flag are deprecated, and will be removed in a future release. The default behavior (proxy streaming requests through the kubelet) will be the only supported option.
  If you are setting `--redirect-container-streaming=true`, then you must migrate off this configuration. The flag will no longer be able to be enabled starting in v1.20. If you are not setting the flag, no action is necessary. ([#88290](https://github.com/kubernetes/kubernetes/pull/88290), [@tallclair](https://github.com/tallclair)) [SIG API Machinery and Node]

- `kubectl` no longer defaults to `http://localhost:8080`.  If you own one of these legacy clusters, you are *strongly- encouraged to secure your server.   If you cannot secure your server, you can set `KUBERNETES_MASTER` if you were relying on that behavior and you're client-go user. Set `--server`, `--kubeconfig` or `KUBECONFIG` to make it work in `kubectl`. ([#86173](https://github.com/kubernetes/kubernetes/pull/86173), [@soltysh](https://github.com/soltysh)) [SIG API Machinery, CLI and Testing]

## Changes by Kind

### Deprecation

- AlgorithmSource is removed from v1alpha2 Scheduler ComponentConfig ([#87999](https://github.com/kubernetes/kubernetes/pull/87999), [@damemi](https://github.com/damemi)) [SIG Scheduling]
- Azure service annotation service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset has been deprecated. Its support would be removed in a future release. ([#88462](https://github.com/kubernetes/kubernetes/pull/88462), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Kube-proxy: deprecate `--healthz-port` and `--metrics-port` flag, please use `--healthz-bind-address` and `--metrics-bind-address` instead ([#88512](https://github.com/kubernetes/kubernetes/pull/88512), [@SataQiu](https://github.com/SataQiu)) [SIG Network]
- Kubeadm: command line option "kubelet-version" for `kubeadm upgrade node` has been deprecated and will be removed in a future release. ([#87942](https://github.com/kubernetes/kubernetes/pull/87942), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle]
- Kubeadm: deprecate the usage of the experimental flag '--use-api' under the 'kubeadm alpha certs renew' command. ([#88827](https://github.com/kubernetes/kubernetes/pull/88827), [@neolit123](https://github.com/neolit123)) [SIG Cluster Lifecycle]
- Kubeadm: kube-dns is deprecated and will not be supported in a future version ([#86574](https://github.com/kubernetes/kubernetes/pull/86574), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle]
- Marked scheduler's metrics scheduling_algorithm_predicate_evaluation_seconds and
  scheduling_algorithm_priority_evaluation_seconds as deprecated. Those are replaced by framework_extension_point_duration_seconds[extenstion_point="Filter"] and framework_extension_point_duration_seconds[extenstion_point="Score"] respectively. ([#86584](https://github.com/kubernetes/kubernetes/pull/86584), [@xiaoanyunfei](https://github.com/xiaoanyunfei)) [SIG Scheduling]
- Marked scheduler's scheduling_duration_seconds Summary metric as deprecated ([#86586](https://github.com/kubernetes/kubernetes/pull/86586), [@xiaoanyunfei](https://github.com/xiaoanyunfei)) [SIG Scheduling]
- Remove all the generators from kubectl run. It will now only create pods. Additionally, deprecates all the flags that are not relevant anymore. ([#87077](https://github.com/kubernetes/kubernetes/pull/87077), [@soltysh](https://github.com/soltysh)) [SIG Architecture, CLI and Testing]
- Remove cluster-monitoring addon ([#85512](https://github.com/kubernetes/kubernetes/pull/85512), [@serathius](https://github.com/serathius)) [SIG Cluster Lifecycle, Instrumentation, Scalability and Testing]
- `AlwaysCheckAllPredicates` is deprecated in scheduler Policy API. ([#86369](https://github.com/kubernetes/kubernetes/pull/86369), [@Huang-Wei](https://github.com/Huang-Wei)) [SIG Scheduling]

### API Change

- --enable-cadvisor-endpoints is now disabled by default. If you need access to the cAdvisor v1 Json API please enable it explicitly in the kubelet command line. Please note that this flag was deprecated in 1.15 and will be removed in 1.19. ([#87440](https://github.com/kubernetes/kubernetes/pull/87440), [@dims](https://github.com/dims)) [SIG Instrumentation, Node and Testing]
- A new IngressClass resource has been added to enable better Ingress configuration. ([#88509](https://github.com/kubernetes/kubernetes/pull/88509), [@robscott](https://github.com/robscott)) [SIG API Machinery, Apps, CLI, Network, Node and Testing]
- API additions to apiserver types ([#87179](https://github.com/kubernetes/kubernetes/pull/87179), [@Jefftree](https://github.com/Jefftree)) [SIG API Machinery, Cloud Provider and Cluster Lifecycle]
- Add Scheduling Profiles to kubescheduler.config.k8s.io/v1alpha2 ([#88087](https://github.com/kubernetes/kubernetes/pull/88087), [@alculquicondor](https://github.com/alculquicondor)) [SIG Scheduling and Testing]
- Add kube-proxy flags --ipvs-tcp-timeout, --ipvs-tcpfin-timeout, --ipvs-udp-timeout to configure IPVS connection timeouts. ([#85517](https://github.com/kubernetes/kubernetes/pull/85517), [@andrewsykim](https://github.com/andrewsykim)) [SIG Cluster Lifecycle and Network]
- Add kubescheduler.config.k8s.io/v1alpha2 ([#87628](https://github.com/kubernetes/kubernetes/pull/87628), [@alculquicondor](https://github.com/alculquicondor)) [SIG Scheduling]
- Added GenericPVCDataSource feature gate to enable using arbitrary custom resources as the data source for a PVC. ([#88636](https://github.com/kubernetes/kubernetes/pull/88636), [@bswartz](https://github.com/bswartz)) [SIG Apps and Storage]
- Added support for multiple sizes huge pages on a container level ([#84051](https://github.com/kubernetes/kubernetes/pull/84051), [@bart0sh](https://github.com/bart0sh)) [SIG Apps, Node and Storage]
- Added the HPA API, that allows scale behavior to be configured through the HPA
  `behavior` field. Behaviors are specified separately for scaling up and down. In
  each direction a stabilization window can be specified as well as a list of
  policies and how to select amongst them. Policies can limit the absolute number
  of pods added or removed, or the percentage of pods added or removed. ([#74525](https://github.com/kubernetes/kubernetes/pull/74525), [@gliush](https://github.com/gliush)) [SIG API Machinery, Apps, Autoscaling and CLI]
- Adds alpha OIDC discovery support for the service account token issuer. Offers `/.well-known/openid-configuration` and `/openid/v1/jwks` paths on the API server. These paths are subject to the usual authorization for `nonResourceURLs`. The associated feature gate is `ServiceAccountIssuerDiscovery` and is disabled by default due to the alpha feature status. ([#80724](https://github.com/kubernetes/kubernetes/pull/80724), [@cceckman](https://github.com/cceckman)) [SIG API Machinery, Auth, Cluster Lifecycle and Testing]
- Allow user to specify fsgroup permission change policy for pods ([#88488](https://github.com/kubernetes/kubernetes/pull/88488), [@gnufied](https://github.com/gnufied)) [SIG Apps and Storage]
- AppProtocol is a new field on Service and Endpoints resources, enabled with the ServiceAppProtocol feature gate. ([#88503](https://github.com/kubernetes/kubernetes/pull/88503), [@robscott](https://github.com/robscott)) [SIG Apps and Network]
- BlockVolume and CSIBlockVolume features are now GA. ([#88673](https://github.com/kubernetes/kubernetes/pull/88673), [@jsafrane](https://github.com/jsafrane)) [SIG Apps, Node and Storage]
- CSI Migration migration annotation key is now `kubernetes.io/migrated-to` ([#88032](https://github.com/kubernetes/kubernetes/pull/88032), [@davidz627](https://github.com/davidz627)) [SIG API Machinery, Apps and Cluster Lifecycle]
- Consumers of the 'certificatesigningrequests/approval' API must now grant permission to 'approve' CSRs for the 'signerName' specified on the CSR. More information on the new signerName field can be found at https://github.com/kubernetes/enhancements/blob/master/keps/sig-auth/20190607-certificates-api.md&#35;signers ([#88246](https://github.com/kubernetes/kubernetes/pull/88246), [@munnerz](https://github.com/munnerz)) [SIG API Machinery, Apps, Auth, CLI, Node and Testing]
- CustomResourceDefinition schemas that use `x-kubernetes-list-map-keys` to specify properties that uniquely identify list items must make those properties required or have a default value, to ensure those properties are present for all list items. See https://kubernetes.io/docs/reference/using-api/api-concepts/&#35;merge-strategy for details. ([#88076](https://github.com/kubernetes/kubernetes/pull/88076), [@eloyekunle](https://github.com/eloyekunle)) [SIG API Machinery and Testing]
- CustomResourceDefinition status fields are no longer required for client validation when submitting manifests. ([#87213](https://github.com/kubernetes/kubernetes/pull/87213), [@hasheddan](https://github.com/hasheddan)) [SIG API Machinery]
- Fixed missing validation of uniqueness of list items in lists with `x-kubernetes-list-type: map` or x-kubernetes-list-type: set` in CustomResources. ([#84920](https://github.com/kubernetes/kubernetes/pull/84920), [@sttts](https://github.com/sttts)) [SIG API Machinery]
- Fixes a regression with clients prior to 1.15 not being able to update podIP in pod status, or podCIDR in node spec, against >= 1.16 API servers ([#88505](https://github.com/kubernetes/kubernetes/pull/88505), [@liggitt](https://github.com/liggitt)) [SIG Apps and Network]
- HardPodAffinityWeight has to be configured via PluginConfig in kubescheduler.config.k8s.io/v1alpha2 ([#88002](https://github.com/kubernetes/kubernetes/pull/88002), [@alculquicondor](https://github.com/alculquicondor)) [SIG Scheduling and Testing]
- Ingress: Add Exact and Prefix maching to Ingress PathTypes ([#88587](https://github.com/kubernetes/kubernetes/pull/88587), [@cmluciano](https://github.com/cmluciano)) [SIG Apps, Cluster Lifecycle and Network]
- Ingress: Add alternate backends via TypedLocalObjectReference ([#88775](https://github.com/kubernetes/kubernetes/pull/88775), [@cmluciano](https://github.com/cmluciano)) [SIG Apps and Network]
- Ingress: allow wildcard hosts in IngressRule ([#88858](https://github.com/kubernetes/kubernetes/pull/88858), [@cmluciano](https://github.com/cmluciano)) [SIG Network]
- Introduce Alpha field `Immutable` in both Secret and ConfigMap objects to mark their contents as immutable. The implementation is hidden behind feature gate `ImmutableEphemeralVolumes` (currently in Alpha stage). ([#86377](https://github.com/kubernetes/kubernetes/pull/86377), [@wojtek-t](https://github.com/wojtek-t)) [SIG Apps, CLI and Testing]
- Introduces optional --detect-local flag to kube-proxy. 
  Currently the only supported value is "cluster-cidr", 
  which is the default if not specified. ([#87748](https://github.com/kubernetes/kubernetes/pull/87748), [@satyasm](https://github.com/satyasm)) [SIG Cluster Lifecycle, Network and Scheduling]
- Kube-apiserver no longer serves the following deprecated APIs:
  - All resources under `apps/v1beta1` and `apps/v1beta2` - use `apps/v1` instead
  - `daemonsets`, `deployments`, `replicasets` resources under `extensions/v1beta1` - use `apps/v1` instead
  - `networkpolicies` resources under `extensions/v1beta1` - use `networking.k8s.io/v1` instead
  - `podsecuritypolicies` resources under `extensions/v1beta1` - use `policy/v1beta1` instead ([#85903](https://github.com/kubernetes/kubernetes/pull/85903), [@liggitt](https://github.com/liggitt)) [SIG API Machinery, Apps, Cluster Lifecycle, Instrumentation and Testing]
- Kube-controller-manager and kube-scheduler expose profiling by default to match the kube-apiserver.  Use `--enable-profiling=false` to disable. ([#88663](https://github.com/kubernetes/kubernetes/pull/88663), [@deads2k](https://github.com/deads2k)) [SIG API Machinery, Cloud Provider and Scheduling]
- Kube-scheduler can run more than one scheduling profile. Given a pod, the profile is selected by using its `.spec.SchedulerName`. ([#88285](https://github.com/kubernetes/kubernetes/pull/88285), [@alculquicondor](https://github.com/alculquicondor)) [SIG Apps, Scheduling and Testing]
- Kubelet podresources API now provides the information about active pods only. ([#79409](https://github.com/kubernetes/kubernetes/pull/79409), [@takmatsu](https://github.com/takmatsu)) [SIG Node]
- Move TaintBasedEvictions feature gates to GA ([#87487](https://github.com/kubernetes/kubernetes/pull/87487), [@skilxn-go](https://github.com/skilxn-go)) [SIG API Machinery, Apps, Node, Scheduling and Testing]
- Moving Windows RunAsUserName feature to GA ([#87790](https://github.com/kubernetes/kubernetes/pull/87790), [@marosset](https://github.com/marosset)) [SIG Apps and Windows]
- New flag --endpointslice-updates-batch-period in kube-controller-manager can be used to reduce number of endpointslice updates generated by pod changes. ([#88745](https://github.com/kubernetes/kubernetes/pull/88745), [@mborsz](https://github.com/mborsz)) [SIG API Machinery, Apps and Network]
- New flag `--show-hidden-metrics-for-version` in kube-proxy can be used to show all hidden metrics that deprecated in the previous minor release. ([#85279](https://github.com/kubernetes/kubernetes/pull/85279), [@RainbowMango](https://github.com/RainbowMango)) [SIG Cluster Lifecycle and Network]
- New flag `--show-hidden-metrics-for-version` in kubelet can be used to show all hidden metrics that deprecated in the previous minor release. ([#85282](https://github.com/kubernetes/kubernetes/pull/85282), [@serathius](https://github.com/serathius)) [SIG Node]
- Promote StartupProbe to beta for 1.18 release ([#83437](https://github.com/kubernetes/kubernetes/pull/83437), [@matthyx](https://github.com/matthyx)) [SIG Node, Scalability and Testing]
- Remove deprecated fields from .leaderElection in kubescheduler.config.k8s.io/v1alpha2 ([#87904](https://github.com/kubernetes/kubernetes/pull/87904), [@alculquicondor](https://github.com/alculquicondor)) [SIG Scheduling]
- Removes ConfigMap as suggestion for IngressClass parameters ([#89093](https://github.com/kubernetes/kubernetes/pull/89093), [@robscott](https://github.com/robscott)) [SIG Network]
- Return &corev1.LimitRange{
  		ObjectMeta: metadata,
  		Spec: corev1.LimitRangeSpec{
  			Limits: []corev1.LimitRangeItem{
  				{
                                          Type: "Container",
  					Max: corev1.ResourceList{corev1.ResourceCPU: resource.MustParse("2.5")},
  					Default: corev1.ResourceList{corev1.ResourceCPU: resource.MustParse("2.5")},
  					DefaultRequest: corev1.ResourceList{corev1.ResourceCPU: resource.MustParse("2.5")},
  				},
  			},
  		},
  	} ([#88029](https://github.com/kubernetes/kubernetes/pull/88029), [@bismitaguha](https://github.com/bismitaguha)) [SIG Node]
- Scheduler Extenders can now be configured in the v1alpha2 component config ([#88768](https://github.com/kubernetes/kubernetes/pull/88768), [@damemi](https://github.com/damemi)) [SIG Release, Scheduling and Testing]
- The PostFilter of scheduler framework is renamed to PreScore. ([#87751](https://github.com/kubernetes/kubernetes/pull/87751), [@skilxn-go](https://github.com/skilxn-go)) [SIG Scheduling and Testing]
- The apiserver/v1alph1&#35;EgressSelectorConfiguration API is now beta. ([#88502](https://github.com/kubernetes/kubernetes/pull/88502), [@caesarxuchao](https://github.com/caesarxuchao)) [SIG API Machinery]
- The following feature gates are removed, because the associated features were unconditionally enabled in previous releases: CustomResourceValidation, CustomResourceSubresources, CustomResourceWebhookConversion, CustomResourcePublishOpenAPI, CustomResourceDefaulting ([#87475](https://github.com/kubernetes/kubernetes/pull/87475), [@liggitt](https://github.com/liggitt)) [SIG API Machinery]
- The k8s.io/node-api component is no longer updated. Instead, use the RuntimeClass types located within k8s.io/api, and the generated clients located within k8s.io/client-go ([#87503](https://github.com/kubernetes/kubernetes/pull/87503), [@liggitt](https://github.com/liggitt)) [SIG Node and Release]
- The storage.k8s.io/CSIDriver has moved to GA, and is now available for use. ([#84814](https://github.com/kubernetes/kubernetes/pull/84814), [@huffmanca](https://github.com/huffmanca)) [SIG API Machinery, Apps, Auth, Node, Scheduling, Storage and Testing]
- VolumePVCDataSource moves to GA in 1.18 release ([#88686](https://github.com/kubernetes/kubernetes/pull/88686), [@j-griffith](https://github.com/j-griffith)) [SIG Apps, CLI and Cluster Lifecycle]

### Feature

- API request throttling (due to a high rate of requests) is now reported in client-go logs at log level 2.  The messages are of the form
  
  Throttling request took 1.50705208s, request: GET:<URL>
  
  The presence of these messages, may indicate to the administrator the need to tune the cluster accordingly. ([#87740](https://github.com/kubernetes/kubernetes/pull/87740), [@jennybuckley](https://github.com/jennybuckley)) [SIG API Machinery]
- API request throttling (due to a high rate of requests) is now reported in the kubelet (and other component) logs by default.  The messages are of the form
  
  Throttling request took 1.50705208s, request: GET:<URL>
  
  The presence of large numbers of these messages, particularly with long delay times, may indicate to the administrator the need to tune the cluster accordingly. ([#80649](https://github.com/kubernetes/kubernetes/pull/80649), [@RobertKrawitz](https://github.com/RobertKrawitz)) [SIG API Machinery and Node]
- Add --dry-run to kubectl delete, taint, replace ([#88292](https://github.com/kubernetes/kubernetes/pull/88292), [@julianvmodesto](https://github.com/julianvmodesto)) [SIG CLI and Testing]
- Add VolumeBinder method to FrameworkHandle interface, which allows user to get the volume binder when implementing scheduler framework plugins. ([#86940](https://github.com/kubernetes/kubernetes/pull/86940), [@skilxn-go](https://github.com/skilxn-go)) [SIG Scheduling]
- Add `rest_client_rate_limiter_duration_seconds` metric to component-base to track client side rate limiter latency in seconds. Broken down by verb and URL. ([#88134](https://github.com/kubernetes/kubernetes/pull/88134), [@jennybuckley](https://github.com/jennybuckley)) [SIG API Machinery, Cluster Lifecycle and Instrumentation]
- Add huge page stats to Allocated resources in "kubectl describe node" ([#80605](https://github.com/kubernetes/kubernetes/pull/80605), [@odinuge](https://github.com/odinuge)) [SIG CLI]
- Add indexer for storage cacher ([#85445](https://github.com/kubernetes/kubernetes/pull/85445), [@shaloulcy](https://github.com/shaloulcy)) [SIG API Machinery]
- Add support for mount options to the FC volume plugin ([#87499](https://github.com/kubernetes/kubernetes/pull/87499), [@ejweber](https://github.com/ejweber)) [SIG Storage]
- Add support for pre-allocated hugepages for more than one page size ([#82820](https://github.com/kubernetes/kubernetes/pull/82820), [@odinuge](https://github.com/odinuge)) [SIG Apps]
- Added a config-mode flag in azure auth module to enable getting AAD token without spn: prefix in audience claim. When it's not specified, the default behavior doesn't change. ([#87630](https://github.com/kubernetes/kubernetes/pull/87630), [@weinong](https://github.com/weinong)) [SIG API Machinery, Auth, CLI and Cloud Provider]
- Added more details to taint toleration errors ([#87250](https://github.com/kubernetes/kubernetes/pull/87250), [@starizard](https://github.com/starizard)) [SIG Apps and Scheduling]
- Added two client certificate metrics for exec auth:
      - `rest_client_certificate_expiration_seconds` a gauge reporting the lifetime of the current client certificate. Reports the time of expiry in seconds since January 1, 1970 UTC.
      - `rest_client_certificate_rotation_age` a histogram reporting the age of a just rotated client certificate in seconds. ([#84382](https://github.com/kubernetes/kubernetes/pull/84382), [@sambdavidson](https://github.com/sambdavidson)) [SIG API Machinery, Auth, Cluster Lifecycle and Instrumentation]
- Adds support for NodeCIDR as an argument to --detect-local-mode ([#88935](https://github.com/kubernetes/kubernetes/pull/88935), [@satyasm](https://github.com/satyasm)) [SIG Network]
- Aggregation api will have alpha support for network proxy ([#87515](https://github.com/kubernetes/kubernetes/pull/87515), [@Sh4d1](https://github.com/Sh4d1)) [SIG API Machinery]
- Allow for configuration of CoreDNS replica count ([#85837](https://github.com/kubernetes/kubernetes/pull/85837), [@pickledrick](https://github.com/pickledrick)) [SIG Cluster Lifecycle]
- Allow user to specify resource using --filename flag when invoking kubectl exec ([#88460](https://github.com/kubernetes/kubernetes/pull/88460), [@soltysh](https://github.com/soltysh)) [SIG CLI and Testing]
- Apiserver add a new flag --goaway-chance which is the fraction of requests that will be closed gracefully(GOAWAY) to prevent HTTP/2 clients from getting stuck on a single apiserver. 
  After the connection closed(received GOAWAY), the client's other in-flight requests won't be affected, and the client will reconnect. 
  The flag min value is 0 (off), max is .02 (1/50 requests); .001 (1/1000) is a recommended starting point.
  Clusters with single apiservers, or which don't use a load balancer, should NOT enable this. ([#88567](https://github.com/kubernetes/kubernetes/pull/88567), [@answer1991](https://github.com/answer1991)) [SIG API Machinery]
- Azure Cloud Provider now supports using Azure network resources (Virtual Network, Load Balancer, Public IP, Route Table, Network Security Group, etc.) in different AAD Tenant and Subscription than those for the Kubernetes cluster. To use the feature, please reference https://github.com/kubernetes-sigs/cloud-provider-azure/blob/master/docs/cloud-provider-config.md&#35;host-network-resources-in-different-aad-tenant-and-subscription. ([#88384](https://github.com/kubernetes/kubernetes/pull/88384), [@bowen5](https://github.com/bowen5)) [SIG Cloud Provider]
- Azure VMSS/VMSSVM clients now suppress requests on throttling ([#86740](https://github.com/kubernetes/kubernetes/pull/86740), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Azure cloud provider cache TTL is configurable, list of the azure cloud provider is as following:
  - "availabilitySetNodesCacheTTLInSeconds"
  - "vmssCacheTTLInSeconds"
  - "vmssVirtualMachinesCacheTTLInSeconds"
  - "vmCacheTTLInSeconds"
  - "loadBalancerCacheTTLInSeconds"
  - "nsgCacheTTLInSeconds"
  - "routeTableCacheTTLInSeconds" ([#86266](https://github.com/kubernetes/kubernetes/pull/86266), [@zqingqing1](https://github.com/zqingqing1)) [SIG Cloud Provider]
- Azure global rate limit is switched to per-client. A set of new rate limit configure options are introduced, including routeRateLimit, SubnetsRateLimit, InterfaceRateLimit, RouteTableRateLimit, LoadBalancerRateLimit, PublicIPAddressRateLimit, SecurityGroupRateLimit, VirtualMachineRateLimit, StorageAccountRateLimit, DiskRateLimit, SnapshotRateLimit, VirtualMachineScaleSetRateLimit and VirtualMachineSizeRateLimit.
  
  The original rate limit options would be default values for those new client's rate limiter. ([#86515](https://github.com/kubernetes/kubernetes/pull/86515), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Azure network and VM clients now suppress requests on throttling ([#87122](https://github.com/kubernetes/kubernetes/pull/87122), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Azure storage clients now suppress requests on throttling ([#87306](https://github.com/kubernetes/kubernetes/pull/87306), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Azure: add support for single stack IPv6 ([#88448](https://github.com/kubernetes/kubernetes/pull/88448), [@aramase](https://github.com/aramase)) [SIG Cloud Provider]
- Controller manager serve workqueue metrics ([#87967](https://github.com/kubernetes/kubernetes/pull/87967), [@zhan849](https://github.com/zhan849)) [SIG API Machinery]
- DefaultConstraints can be specified for the PodTopologySpread plugin in the component config ([#88671](https://github.com/kubernetes/kubernetes/pull/88671), [@alculquicondor](https://github.com/alculquicondor)) [SIG Scheduling]
- DisableAvailabilitySetNodes is added to avoid VM list for VMSS clusters. It should only be used when vmType is "vmss" and all the nodes (including masters) are VMSS virtual machines. ([#87685](https://github.com/kubernetes/kubernetes/pull/87685), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Elasticsearch supports automatically setting the advertise address ([#85944](https://github.com/kubernetes/kubernetes/pull/85944), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle and Instrumentation]
- EndpointSlices will now be enabled by default. A new `EndpointSliceProxying` feature gate determines if kube-proxy will use EndpointSlices, this is disabled by default. ([#86137](https://github.com/kubernetes/kubernetes/pull/86137), [@robscott](https://github.com/robscott)) [SIG Network]
- Following metrics have been turned off:
  - kubelet_pod_worker_latency_microseconds
  - kubelet_pod_start_latency_microseconds
  - kubelet_cgroup_manager_latency_microseconds
  - kubelet_pod_worker_start_latency_microseconds
  - kubelet_pleg_relist_latency_microseconds
  - kubelet_pleg_relist_interval_microseconds
  - kubelet_eviction_stats_age_microseconds
  - kubelet_runtime_operations
  - kubelet_runtime_operations_latency_microseconds
  - kubelet_runtime_operations_errors
  - kubelet_device_plugin_registration_count
  - kubelet_device_plugin_alloc_latency_microseconds
  - kubelet_docker_operations
  - kubelet_docker_operations_latency_microseconds
  - kubelet_docker_operations_errors
  - kubelet_docker_operations_timeout
  - network_plugin_operations_latency_microseconds ([#83841](https://github.com/kubernetes/kubernetes/pull/83841), [@RainbowMango](https://github.com/RainbowMango)) [SIG Network and Node]
- Hollow-node use fake CRI ([#85879](https://github.com/kubernetes/kubernetes/pull/85879), [@gongguan](https://github.com/gongguan)) [SIG Node and Scalability]
- Introduced BackoffManager interface for backoff management ([#87829](https://github.com/kubernetes/kubernetes/pull/87829), [@zhan849](https://github.com/zhan849)) [SIG API Machinery]
- Kube-apiserver metrics will now include request counts, latencies, and response sizes for /healthz, /livez, and /readyz requests. ([#83598](https://github.com/kubernetes/kubernetes/pull/83598), [@jktomer](https://github.com/jktomer)) [SIG API Machinery]
- Kube-apiserver, kube-scheduler and kube-controller manager now use SO_REUSEPORT socket option when listening on address defined by --bind-address and --secure-port flags, when running on Unix systems (Windows is NOT supported). This allows to run multiple instances of those processes on a single host with the same configuration, which allows to update/restart them in a graceful way, without causing downtime. ([#88893](https://github.com/kubernetes/kubernetes/pull/88893), [@invidian](https://github.com/invidian)) [SIG API Machinery, Scheduling and Testing]
- Kube-proxy: Added dual-stack IPv4/IPv6 support to the iptables proxier. ([#82462](https://github.com/kubernetes/kubernetes/pull/82462), [@vllry](https://github.com/vllry)) [SIG Network]
- Kubeadm now supports automatic calculations of dual-stack node cidr masks to kube-controller-manager. ([#85609](https://github.com/kubernetes/kubernetes/pull/85609), [@Arvinderpal](https://github.com/Arvinderpal)) [SIG Cluster Lifecycle]
- Kubeadm: The ClusterStatus struct present in the kubeadm-config ConfigMap is deprecated and will be removed on a future version. It is going to be maintained by kubeadm until it gets removed. The same information can be found on `etcd` and `kube-apiserver` pod annotations, `kubeadm.kubernetes.io/etcd.advertise-client-urls` and `kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint` respectively. ([#87656](https://github.com/kubernetes/kubernetes/pull/87656), [@ereslibre](https://github.com/ereslibre)) [SIG Cluster Lifecycle]
- Kubeadm: add a upgrade health check that deploys a Job ([#81319](https://github.com/kubernetes/kubernetes/pull/81319), [@neolit123](https://github.com/neolit123)) [SIG Cluster Lifecycle]
- Kubeadm: add the experimental feature gate PublicKeysECDSA that can be used to create a
  cluster with ECDSA certificates from "kubeadm init". Renewal of existing ECDSA certificates is
  also supported using "kubeadm alpha certs renew", but not switching between the RSA and
  ECDSA algorithms on the fly or during upgrades. ([#86953](https://github.com/kubernetes/kubernetes/pull/86953), [@rojkov](https://github.com/rojkov)) [SIG API Machinery, Auth and Cluster Lifecycle]
- Kubeadm: implemented structured output of 'kubeadm config images list' command in JSON, YAML, Go template and JsonPath formats ([#86810](https://github.com/kubernetes/kubernetes/pull/86810), [@bart0sh](https://github.com/bart0sh)) [SIG Cluster Lifecycle]
- Kubeadm: on kubeconfig certificate renewal, keep the embedded CA in sync with the one on disk ([#88052](https://github.com/kubernetes/kubernetes/pull/88052), [@neolit123](https://github.com/neolit123)) [SIG Cluster Lifecycle]
- Kubeadm: reject a node joining the cluster if a node with the same name already exists ([#81056](https://github.com/kubernetes/kubernetes/pull/81056), [@neolit123](https://github.com/neolit123)) [SIG Cluster Lifecycle]
- Kubeadm: reset raises warnings if it cannot delete folders ([#85265](https://github.com/kubernetes/kubernetes/pull/85265), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle]
- Kubeadm: support Windows specific kubelet flags in kubeadm-flags.env ([#88287](https://github.com/kubernetes/kubernetes/pull/88287), [@gab-satchi](https://github.com/gab-satchi)) [SIG Cluster Lifecycle and Windows]
- Kubeadm: support automatic retry after failing to pull image ([#86899](https://github.com/kubernetes/kubernetes/pull/86899), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle]
- Kubeadm: upgrade supports fallback to the nearest known etcd version if an unknown k8s version is passed ([#88373](https://github.com/kubernetes/kubernetes/pull/88373), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle]
- Kubectl cluster-info dump changed to only display a message telling you the location where the output was written when the output is not standard output. ([#88765](https://github.com/kubernetes/kubernetes/pull/88765), [@brianpursley](https://github.com/brianpursley)) [SIG CLI]
- Kubectl/drain: add disable-eviction option.
  Force drain to use delete, even if eviction is supported. This will bypass checking PodDisruptionBudgets, and should be used with caution. ([#85571](https://github.com/kubernetes/kubernetes/pull/85571), [@michaelgugino](https://github.com/michaelgugino)) [SIG CLI]
- Kubectl/drain: add skip-wait-for-delete-timeout option.
  If pod DeletionTimestamp older than N seconds, skip waiting for the pod.  Seconds must be greater than 0 to skip. ([#85577](https://github.com/kubernetes/kubernetes/pull/85577), [@michaelgugino](https://github.com/michaelgugino)) [SIG CLI]
- Kubelet now exports a "server_expiration_renew_failure" and "client_expiration_renew_failure" metric counter if the certificate rotations cannot be performed. ([#84614](https://github.com/kubernetes/kubernetes/pull/84614), [@rphillips](https://github.com/rphillips)) [SIG API Machinery, Auth, CLI, Cloud Provider, Cluster Lifecycle, Instrumentation, Node and Release]
- Kubelet: resource metrics endpoint `/metrics/resource/v1alpha1` as well as all metrics under this endpoint have been deprecated.
  Please convert to the following metrics emitted by endpoint `/metrics/resource`:
  - scrape_error --> scrape_error
  - node_cpu_usage_seconds_total --> node_cpu_usage_seconds
  - node_memory_working_set_bytes --> node_memory_working_set_bytes
  - container_cpu_usage_seconds_total --> container_cpu_usage_seconds
  - container_memory_working_set_bytes --> container_memory_working_set_bytes
  - scrape_error --> scrape_error ([#86282](https://github.com/kubernetes/kubernetes/pull/86282), [@RainbowMango](https://github.com/RainbowMango)) [SIG Node]
- Kubelet: the metric process_start_time_seconds be marked as with the ALPHA stability level. ([#85446](https://github.com/kubernetes/kubernetes/pull/85446), [@RainbowMango](https://github.com/RainbowMango)) [SIG API Machinery, Cluster Lifecycle, Instrumentation and Node]
- New flag `--show-hidden-metrics-for-version` in kube-controller-manager can be used to show all hidden metrics that deprecated in the previous minor release. ([#85281](https://github.com/kubernetes/kubernetes/pull/85281), [@RainbowMango](https://github.com/RainbowMango)) [SIG API Machinery]
- New flag `--show-hidden-metrics-for-version` in kube-scheduler can be used to show all hidden metrics that deprecated in the previous minor release. ([#84913](https://github.com/kubernetes/kubernetes/pull/84913), [@serathius](https://github.com/serathius)) [SIG Instrumentation and Scheduling]
- New metric kubelet_pleg_last_seen_seconds to aid diagnosis of PLEG not healthy issues. ([#86251](https://github.com/kubernetes/kubernetes/pull/86251), [@bboreham](https://github.com/bboreham)) [SIG Node]
- Option `preConfiguredBackendPoolLoadBalancerTypes` is added to azure cloud provider for the pre-configured load balancers, possible values: `""`, `"internal"`, "external"`, `"all"` ([#86338](https://github.com/kubernetes/kubernetes/pull/86338), [@gossion](https://github.com/gossion)) [SIG Cloud Provider]
- PodTopologySpread plugin now excludes terminatingPods when making scheduling decisions. ([#87845](https://github.com/kubernetes/kubernetes/pull/87845), [@Huang-Wei](https://github.com/Huang-Wei)) [SIG Scheduling]
- Print NotReady when pod is not ready based on its conditions. ([#88240](https://github.com/kubernetes/kubernetes/pull/88240), [@soltysh](https://github.com/soltysh)) [SIG CLI]
- Promote CSIMigrationOpenStack to Beta (off by default since it requires installation of the OpenStack Cinder CSI Driver)
  The in-tree AWS OpenStack Cinder "kubernetes.io/cinder" was already deprecated a while ago and will be removed in 1.20. Users should enable CSIMigration + CSIMigrationOpenStack features and install the OpenStack Cinder CSI Driver (https://github.com/kubernetes-sigs/cloud-provider-openstack) to avoid disruption to existing Pod and PVC objects at that time.
  Users should start using the OpenStack Cinder CSI Driver directly for any new volumes. ([#85637](https://github.com/kubernetes/kubernetes/pull/85637), [@dims](https://github.com/dims)) [SIG Cloud Provider]
- Provider/azure: Network security groups can now be in a separate resource group. ([#87035](https://github.com/kubernetes/kubernetes/pull/87035), [@CecileRobertMichon](https://github.com/CecileRobertMichon)) [SIG Cloud Provider]
- SafeSysctlWhitelist: add net.ipv4.ping_group_range ([#85463](https://github.com/kubernetes/kubernetes/pull/85463), [@AkihiroSuda](https://github.com/AkihiroSuda)) [SIG Auth]
- Scheduler Extender API is now located under k8s.io/kube-scheduler/extender ([#88540](https://github.com/kubernetes/kubernetes/pull/88540), [@damemi](https://github.com/damemi)) [SIG Release, Scheduling and Testing]
- Scheduler framework permit plugins now run at the end of the scheduling cycle, after reserve plugins. Waiting on permit will remain in the beginning of the binding cycle. ([#88199](https://github.com/kubernetes/kubernetes/pull/88199), [@mateuszlitwin](https://github.com/mateuszlitwin)) [SIG Scheduling]
- Scheduler: Add DefaultBinder plugin ([#87430](https://github.com/kubernetes/kubernetes/pull/87430), [@alculquicondor](https://github.com/alculquicondor)) [SIG Scheduling and Testing]
- Signatures on scale client methods have been modified to accept `context.Context` as a first argument. Signatures of Get, Update, and Patch methods have been updated to accept GetOptions, UpdateOptions and PatchOptions respectively. ([#88599](https://github.com/kubernetes/kubernetes/pull/88599), [@julianvmodesto](https://github.com/julianvmodesto)) [SIG API Machinery, Apps, Autoscaling and CLI]
- Signatures on the dynamic client methods have been modified to accept `context.Context` as a first argument. Signatures of Delete and DeleteCollection methods now accept DeleteOptions by value instead of by reference. ([#88906](https://github.com/kubernetes/kubernetes/pull/88906), [@liggitt](https://github.com/liggitt)) [SIG API Machinery, Apps, CLI, Cluster Lifecycle, Storage and Testing]
- Signatures on the metadata client methods have been modified to accept `context.Context` as a first argument. Signatures of Delete and DeleteCollection methods now accept DeleteOptions by value instead of by reference. ([#88910](https://github.com/kubernetes/kubernetes/pull/88910), [@liggitt](https://github.com/liggitt)) [SIG API Machinery, Apps and Testing]
- Skip default spreading scoring plugin for pods that define TopologySpreadConstraints ([#87566](https://github.com/kubernetes/kubernetes/pull/87566), [@skilxn-go](https://github.com/skilxn-go)) [SIG Scheduling]
- Support server-side dry-run in kubectl with --dry-run=server for commands including apply, patch, create, run and expose. ([#87714](https://github.com/kubernetes/kubernetes/pull/87714), [@julianvmodesto](https://github.com/julianvmodesto)) [SIG API Machinery, CLI and Testing]
- The feature PodTopologySpread (featuregate `EvenPodsSpread`) has been enabled by default in 1.18. ([#88105](https://github.com/kubernetes/kubernetes/pull/88105), [@Huang-Wei](https://github.com/Huang-Wei)) [SIG Scheduling and Testing]
- The kubectl --dry-run flag now accepts the values 'client', 'server', and 'none', to support client-side and server-side dry-run strategies. The boolean and unset values for the --dry-run flag are deprecated and a value will be required in a future version. ([#87580](https://github.com/kubernetes/kubernetes/pull/87580), [@julianvmodesto](https://github.com/julianvmodesto)) [SIG CLI]
- The kubelet and the default docker runtime now support running ephemeral containers in the Linux process namespace of a target container. Other container runtimes must implement this feature before it will be available in that runtime. ([#84731](https://github.com/kubernetes/kubernetes/pull/84731), [@verb](https://github.com/verb)) [SIG Node]
- The underlying format of the `CPUManager` state file has changed. Upgrades should be seamless, but any third-party tools that rely on reading the previous format need to be updated. ([#84462](https://github.com/kubernetes/kubernetes/pull/84462), [@klueska](https://github.com/klueska)) [SIG Node and Testing]
- Update CNI version to v0.8.5 ([#78819](https://github.com/kubernetes/kubernetes/pull/78819), [@justaugustus](https://github.com/justaugustus)) [SIG API Machinery, Cluster Lifecycle, Network, Release and Testing]
- Update etcd client side to v3.4.4 ([#89169](https://github.com/kubernetes/kubernetes/pull/89169), [@jingyih](https://github.com/jingyih)) [SIG API Machinery and Cloud Provider]
- Upgrade to azure-sdk v40.2.0 ([#89105](https://github.com/kubernetes/kubernetes/pull/89105), [@andyzhangx](https://github.com/andyzhangx)) [SIG CLI, Cloud Provider, Cluster Lifecycle, Instrumentation, Storage and Testing]
- Webhooks will have alpha support for network proxy ([#85870](https://github.com/kubernetes/kubernetes/pull/85870), [@Jefftree](https://github.com/Jefftree)) [SIG API Machinery, Auth and Testing]
- When client certificate files are provided, reload files for new connections, and close connections when a certificate changes. ([#79083](https://github.com/kubernetes/kubernetes/pull/79083), [@jackkleeman](https://github.com/jackkleeman)) [SIG API Machinery, Auth, Node and Testing]
- When deleting objects using kubectl with the --force flag, you are no longer required to also specify --grace-period=0. ([#87776](https://github.com/kubernetes/kubernetes/pull/87776), [@brianpursley](https://github.com/brianpursley)) [SIG CLI]
- Windows nodes on GCE can use TPM-based authentication to the master. ([#85466](https://github.com/kubernetes/kubernetes/pull/85466), [@pjh](https://github.com/pjh)) [SIG Cluster Lifecycle]
- You can now pass "--node-ip ::" to kubelet to indicate that it should autodetect an IPv6 address to use as the node's primary address. ([#85850](https://github.com/kubernetes/kubernetes/pull/85850), [@danwinship](https://github.com/danwinship)) [SIG Cloud Provider, Network and Node]
- `kubectl` now contains a `kubectl alpha debug` command. This command allows attaching an ephemeral container to a running pod for the purposes of debugging. ([#88004](https://github.com/kubernetes/kubernetes/pull/88004), [@verb](https://github.com/verb)) [SIG CLI]

### Design

- Kubeadm now errors out whenever a not supported component config version is supplied for the kubelet and kube-proxy ([#85639](https://github.com/kubernetes/kubernetes/pull/85639), [@rosti](https://github.com/rosti)) [SIG Cluster Lifecycle]
- The scheduler Permit extension point doesn't return a boolean value in its Allow() and Reject() functions. ([#87936](https://github.com/kubernetes/kubernetes/pull/87936), [@Huang-Wei](https://github.com/Huang-Wei)) [SIG Scheduling]

### Documentation

- Add instructions about how to bring up e2e test cluster ([#85836](https://github.com/kubernetes/kubernetes/pull/85836), [@YangLu1031](https://github.com/YangLu1031)) [SIG Cluster Lifecycle]
- Update Japanese translation for kubectl help ([#86837](https://github.com/kubernetes/kubernetes/pull/86837), [@inductor](https://github.com/inductor)) [SIG CLI and Docs]
- `kubectl plugin` now prints a note how to install krew ([#88577](https://github.com/kubernetes/kubernetes/pull/88577), [@corneliusweig](https://github.com/corneliusweig)) [SIG CLI]

### Failing Test

- E2e-test-framework: add e2e test namespace dump if all tests succeed but the cleanup fails. ([#85542](https://github.com/kubernetes/kubernetes/pull/85542), [@schrodit](https://github.com/schrodit)) [SIG Testing]
- Verify kubelet & kube-proxy can recover after being killed on Windows nodes ([#84886](https://github.com/kubernetes/kubernetes/pull/84886), [@YangLu1031](https://github.com/YangLu1031)) [SIG Apps, Testing and Windows]

### Other (Bug, Cleanup or Flake)

- "kubectl describe statefulsets.apps" prints garbage for rolling update partition ([#85846](https://github.com/kubernetes/kubernetes/pull/85846), [@phil9909](https://github.com/phil9909)) [SIG CLI]
- Renamed Kubelet metric certificate_manager_server_expiration_seconds to certificate_manager_server_ttl_seconds and changed to report the second until expiration at read time rather than absolute time of expiry.
  - Improved accuracy of Kubelet metric rest_client_exec_plugin_ttl_seconds. ([#85874](https://github.com/kubernetes/kubernetes/pull/85874), [@sambdavidson](https://github.com/sambdavidson)) [SIG API Machinery, Auth, Cluster Lifecycle, Instrumentation and Node]
- Update cri-tools to v1.17.0 ([#86305](https://github.com/kubernetes/kubernetes/pull/86305), [@saschagrunert](https://github.com/saschagrunert)) [SIG Cluster Lifecycle and Release]
- Add a event to PV when filesystem on PV does not match actual filesystem on disk ([#86982](https://github.com/kubernetes/kubernetes/pull/86982), [@gnufied](https://github.com/gnufied)) [SIG API Machinery, CLI, Cloud Provider, Cluster Lifecycle, Instrumentation, Node and Storage]
- Add azure disk WriteAccelerator support ([#87945](https://github.com/kubernetes/kubernetes/pull/87945), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider and Storage]
- Add delays between goroutines for vm instance update ([#88094](https://github.com/kubernetes/kubernetes/pull/88094), [@aramase](https://github.com/aramase)) [SIG Cloud Provider]
- Add init containers log to cluster dump info. ([#88324](https://github.com/kubernetes/kubernetes/pull/88324), [@zhouya0](https://github.com/zhouya0)) [SIG CLI]
- Addons: elasticsearch discovery supports IPv6 ([#85543](https://github.com/kubernetes/kubernetes/pull/85543), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle and Instrumentation]
- Adds "volume.beta.kubernetes.io/migrated-to" annotation to PV's and PVC's when they are migrated to signal external provisioners to pick up those objects for Provisioning and Deleting. ([#87098](https://github.com/kubernetes/kubernetes/pull/87098), [@davidz627](https://github.com/davidz627)) [SIG Apps and Storage]
- Adds back support for AlwaysCheckAllPredicates flag. ([#86496](https://github.com/kubernetes/kubernetes/pull/86496), [@ahg-g](https://github.com/ahg-g)) [SIG Scheduling]
- All apiservers log request lines in a more greppable format. ([#87203](https://github.com/kubernetes/kubernetes/pull/87203), [@lavalamp](https://github.com/lavalamp)) [SIG API Machinery]
- Azure VMSS LoadBalancerBackendAddressPools updating has been improved with squential-sync + concurrent-async requests. ([#88699](https://github.com/kubernetes/kubernetes/pull/88699), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Azure cloud provider now obtains AAD token who audience claim will not have spn: prefix ([#87590](https://github.com/kubernetes/kubernetes/pull/87590), [@weinong](https://github.com/weinong)) [SIG Cloud Provider]
- AzureFile and CephFS use new Mount library that prevents logging of sensitive mount options. ([#88684](https://github.com/kubernetes/kubernetes/pull/88684), [@saad-ali](https://github.com/saad-ali)) [SIG API Machinery, CLI, Cloud Provider, Cluster Lifecycle, Instrumentation and Storage]
- Bind dns-horizontal containers to linux nodes to avoid Windows scheduling on kubernetes cluster includes linux nodes and windows nodes ([#83364](https://github.com/kubernetes/kubernetes/pull/83364), [@wawa0210](https://github.com/wawa0210)) [SIG Cluster Lifecycle and Windows]
- Bind kube-dns containers to linux nodes to avoid Windows scheduling ([#83358](https://github.com/kubernetes/kubernetes/pull/83358), [@wawa0210](https://github.com/wawa0210)) [SIG Cluster Lifecycle and Windows]
- Bind metadata-agent containers to linux nodes to avoid Windows scheduling on kubernetes cluster includes linux nodes and windows nodes ([#83363](https://github.com/kubernetes/kubernetes/pull/83363), [@wawa0210](https://github.com/wawa0210)) [SIG Cluster Lifecycle, Instrumentation and Windows]
- Bind metrics-server containers to linux nodes to avoid Windows scheduling on kubernetes cluster includes linux nodes and windows nodes ([#83362](https://github.com/kubernetes/kubernetes/pull/83362), [@wawa0210](https://github.com/wawa0210)) [SIG Cluster Lifecycle, Instrumentation and Windows]
- Bug fixes:
  Make sure we include latest packages node &#35;351 (@caseydavenport) ([#84163](https://github.com/kubernetes/kubernetes/pull/84163), [@david-tigera](https://github.com/david-tigera)) [SIG Cluster Lifecycle]
- Build: Enable kube-cross image-building on K8s Infra ([#88562](https://github.com/kubernetes/kubernetes/pull/88562), [@justaugustus](https://github.com/justaugustus)) [SIG Release and Testing]
- Bump golang/mock version to v1.3.1 ([#87326](https://github.com/kubernetes/kubernetes/pull/87326), [@wawa0210](https://github.com/wawa0210)) [SIG API Machinery, CLI, Cloud Provider, Cluster Lifecycle, Instrumentation and Node]
- CPU limits are now respected for Windows containers. If a node is over-provisioned, no weighting is used - only limits are respected. ([#86101](https://github.com/kubernetes/kubernetes/pull/86101), [@PatrickLang](https://github.com/PatrickLang)) [SIG Node, Testing and Windows]
- Change CounterVec to Counter about  PLEGDiscardEvent ([#86167](https://github.com/kubernetes/kubernetes/pull/86167), [@yiyang5055](https://github.com/yiyang5055)) [SIG Node]
- Changed core_pattern on COS nodes to be an absolute path. ([#86329](https://github.com/kubernetes/kubernetes/pull/86329), [@mml](https://github.com/mml)) [SIG Cluster Lifecycle and Node]
- Cleaned up the output from `kubectl describe CSINode <name>`. ([#85283](https://github.com/kubernetes/kubernetes/pull/85283), [@huffmanca](https://github.com/huffmanca)) [SIG CLI and Storage]
- Client-go certificate manager rotation gained the ability to preserve optional intermediate chains accompanying issued certificates ([#88744](https://github.com/kubernetes/kubernetes/pull/88744), [@jackkleeman](https://github.com/jackkleeman)) [SIG API Machinery and Auth]
- Cloud provider config CloudProviderBackoffMode has been removed since it won't be used anymore. ([#88463](https://github.com/kubernetes/kubernetes/pull/88463), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Conformance image now depends on stretch-slim instead of debian-hyperkube-base as that image is being deprecated and removed. ([#88702](https://github.com/kubernetes/kubernetes/pull/88702), [@dims](https://github.com/dims)) [SIG Cluster Lifecycle, Release and Testing]
- Deprecate --generator flag from kubectl create commands ([#88655](https://github.com/kubernetes/kubernetes/pull/88655), [@soltysh](https://github.com/soltysh)) [SIG CLI]
- During initialization phase (preflight), kubeadm now verifies the presence of the conntrack executable ([#85857](https://github.com/kubernetes/kubernetes/pull/85857), [@hnanni](https://github.com/hnanni)) [SIG Cluster Lifecycle]
- EndpointSlice should not contain endpoints for terminating pods ([#89056](https://github.com/kubernetes/kubernetes/pull/89056), [@andrewsykim](https://github.com/andrewsykim)) [SIG Apps and Network]
- Evictions due to pods breaching their ephemeral storage limits are now recorded by the `kubelet_evictions` metric and can be alerted on. ([#87906](https://github.com/kubernetes/kubernetes/pull/87906), [@smarterclayton](https://github.com/smarterclayton)) [SIG Node]
- FIX: prevent apiserver from panicking when failing to load audit webhook config file ([#88879](https://github.com/kubernetes/kubernetes/pull/88879), [@JoshVanL](https://github.com/JoshVanL)) [SIG API Machinery and Auth]
- Filter published OpenAPI schema by making nullable, required fields non-required in order to avoid kubectl to wrongly reject null values. ([#85722](https://github.com/kubernetes/kubernetes/pull/85722), [@sttts](https://github.com/sttts)) [SIG API Machinery]
- Fix /readyz to return error immediately after a shutdown is initiated, before the --shutdown-delay-duration has elapsed. ([#88911](https://github.com/kubernetes/kubernetes/pull/88911), [@tkashem](https://github.com/tkashem)) [SIG API Machinery]
- Fix API Server potential memory leak issue in processing watch request. ([#85410](https://github.com/kubernetes/kubernetes/pull/85410), [@answer1991](https://github.com/answer1991)) [SIG API Machinery]
- Fix EndpointSlice controller race condition and ensure that it handles external changes to EndpointSlices. ([#85703](https://github.com/kubernetes/kubernetes/pull/85703), [@robscott](https://github.com/robscott)) [SIG Apps and Network]
- Fix IPv6 addresses lost issue in pure ipv6 vsphere environment ([#86001](https://github.com/kubernetes/kubernetes/pull/86001), [@hubv](https://github.com/hubv)) [SIG Cloud Provider]
- Fix LoadBalancer rule checking so that no unexpected LoadBalancer updates are made ([#85990](https://github.com/kubernetes/kubernetes/pull/85990), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Fix a bug in kube-proxy that caused it to crash when using load balancers with a different IP family ([#87117](https://github.com/kubernetes/kubernetes/pull/87117), [@aojea](https://github.com/aojea)) [SIG Network]
- Fix a bug in port-forward: named port not working with service ([#85511](https://github.com/kubernetes/kubernetes/pull/85511), [@oke-py](https://github.com/oke-py)) [SIG CLI]
- Fix a bug in the dual-stack IPVS proxier where stale IPv6 endpoints were not being cleaned up ([#87695](https://github.com/kubernetes/kubernetes/pull/87695), [@andrewsykim](https://github.com/andrewsykim)) [SIG Network]
- Fix a bug that orphan revision cannot be adopted and statefulset cannot be synced ([#86801](https://github.com/kubernetes/kubernetes/pull/86801), [@likakuli](https://github.com/likakuli)) [SIG Apps]
- Fix a bug where ExternalTrafficPolicy is not applied to service ExternalIPs. ([#88786](https://github.com/kubernetes/kubernetes/pull/88786), [@freehan](https://github.com/freehan)) [SIG Network]
- Fix a bug where kubenet fails to parse the tc output. ([#83572](https://github.com/kubernetes/kubernetes/pull/83572), [@chendotjs](https://github.com/chendotjs)) [SIG Network]
- Fix a regression in kubenet that prevent pods to obtain ip addresses ([#85993](https://github.com/kubernetes/kubernetes/pull/85993), [@chendotjs](https://github.com/chendotjs)) [SIG Network and Node]
- Fix azure file AuthorizationFailure ([#85475](https://github.com/kubernetes/kubernetes/pull/85475), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider and Storage]
- Fix bug where EndpointSlice controller would attempt to modify shared objects. ([#85368](https://github.com/kubernetes/kubernetes/pull/85368), [@robscott](https://github.com/robscott)) [SIG API Machinery, Apps and Network]
- Fix describe ingress annotations not sorted. ([#88394](https://github.com/kubernetes/kubernetes/pull/88394), [@zhouya0](https://github.com/zhouya0)) [SIG CLI]
- Fix detection of SystemOOMs in which the victim is a container. ([#88871](https://github.com/kubernetes/kubernetes/pull/88871), [@dashpole](https://github.com/dashpole)) [SIG Node]
- Fix handling of aws-load-balancer-security-groups annotation. Security-Groups assigned with this annotation are no longer modified by kubernetes which is the expected behaviour of most users. Also no unnecessary Security-Groups are created anymore if this annotation is used. ([#83446](https://github.com/kubernetes/kubernetes/pull/83446), [@Elias481](https://github.com/Elias481)) [SIG Cloud Provider]
- Fix help for api-versions ([#88084](https://github.com/kubernetes/kubernetes/pull/88084), [@soltysh](https://github.com/soltysh)) [SIG CLI]
- Fix invalid VMSS updates due to incorrect cache ([#89002](https://github.com/kubernetes/kubernetes/pull/89002), [@ArchangelSDY](https://github.com/ArchangelSDY)) [SIG Cloud Provider]
- Fix isCurrentInstance for Windows by removing the dependency of hostname. ([#89138](https://github.com/kubernetes/kubernetes/pull/89138), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Fix issue &#35;85805 about resource not found in azure cloud provider when lb specified in other resource group. ([#86502](https://github.com/kubernetes/kubernetes/pull/86502), [@levimm](https://github.com/levimm)) [SIG Cloud Provider]
- Fix kube-apiserver startup to wait for APIServices to be installed into the HTTP handler before reporting readiness. ([#89147](https://github.com/kubernetes/kubernetes/pull/89147), [@sttts](https://github.com/sttts)) [SIG API Machinery]
- Fix kube-proxy dual-stack configuration validation ([#87353](https://github.com/kubernetes/kubernetes/pull/87353), [@aojea](https://github.com/aojea)) [SIG Network]
- Fix kubectl annotate error when local=true is set ([#86952](https://github.com/kubernetes/kubernetes/pull/86952), [@zhouya0](https://github.com/zhouya0)) [SIG CLI]
- Fix kubectl create deployment image name ([#86636](https://github.com/kubernetes/kubernetes/pull/86636), [@zhouya0](https://github.com/zhouya0)) [SIG CLI]
- Fix kubectl drain ignore daemonsets and others. ([#87361](https://github.com/kubernetes/kubernetes/pull/87361), [@zhouya0](https://github.com/zhouya0)) [SIG CLI]
- Fix missing "apiVersion" for "involvedObject" in Events for Nodes. ([#87537](https://github.com/kubernetes/kubernetes/pull/87537), [@uthark](https://github.com/uthark)) [SIG Apps and Node]
- Fix nil pointer dereference in azure cloud provider ([#85975](https://github.com/kubernetes/kubernetes/pull/85975), [@ldx](https://github.com/ldx)) [SIG Cloud Provider]
- Fix regression in statefulset conversion which prevented applying a statefulset multiple times. ([#87706](https://github.com/kubernetes/kubernetes/pull/87706), [@liggitt](https://github.com/liggitt)) [SIG Apps and Testing]
- Fix route conflicted operations when updating multiple routes together ([#88209](https://github.com/kubernetes/kubernetes/pull/88209), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Fix that prevents repeated fetching of PVC/PV objects by kubelet when processing of pod volumes fails. While this prevents hammering API server in these error scenarios, it means that some errors in processing volume(s) for a pod could now take up to 2-3 minutes before retry. ([#88141](https://github.com/kubernetes/kubernetes/pull/88141), [@tedyu](https://github.com/tedyu)) [SIG Node and Storage]
- Fix the bug PIP's DNS is deleted if no DNS label service annotation isn't set. ([#87246](https://github.com/kubernetes/kubernetes/pull/87246), [@nilo19](https://github.com/nilo19)) [SIG Cloud Provider]
- Fix the masters rolling upgrade causing thundering herd of LISTs on etcd leading to control plane unavailability. ([#86430](https://github.com/kubernetes/kubernetes/pull/86430), [@wojtek-t](https://github.com/wojtek-t)) [SIG API Machinery, Node and Testing]
- Fix: add azure disk migration support for CSINode ([#88014](https://github.com/kubernetes/kubernetes/pull/88014), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider and Storage]
- Fix: add non-retriable errors in azure clients ([#87941](https://github.com/kubernetes/kubernetes/pull/87941), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider]
- Fix: add remediation in azure disk attach/detach ([#88444](https://github.com/kubernetes/kubernetes/pull/88444), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider]
- Fix: azure data disk should use same key as os disk by default ([#86351](https://github.com/kubernetes/kubernetes/pull/86351), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider]
- Fix: azure disk could not mounted on Standard_DC4s/DC2s instances ([#86612](https://github.com/kubernetes/kubernetes/pull/86612), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider and Storage]
- Fix: azure file mount timeout issue ([#88610](https://github.com/kubernetes/kubernetes/pull/88610), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider and Storage]
- Fix: check disk status before delete azure disk ([#88360](https://github.com/kubernetes/kubernetes/pull/88360), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider]
- Fix: corrupted mount point in csi driver ([#88569](https://github.com/kubernetes/kubernetes/pull/88569), [@andyzhangx](https://github.com/andyzhangx)) [SIG Storage]
- Fix: get azure disk lun timeout issue ([#88158](https://github.com/kubernetes/kubernetes/pull/88158), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider and Storage]
- Fix: update azure disk max count ([#88201](https://github.com/kubernetes/kubernetes/pull/88201), [@andyzhangx](https://github.com/andyzhangx)) [SIG Cloud Provider and Storage]
- Fixed "requested device X but found Y" attach error on AWS. ([#85675](https://github.com/kubernetes/kubernetes/pull/85675), [@jsafrane](https://github.com/jsafrane)) [SIG Cloud Provider and Storage]
- Fixed NetworkPolicy validation that Except values are accepted when they are outside the CIDR range. ([#86578](https://github.com/kubernetes/kubernetes/pull/86578), [@tnqn](https://github.com/tnqn)) [SIG Network]
- Fixed a bug in the TopologyManager. Previously, the TopologyManager would only guarantee alignment if container creation was serialized in some way. Alignment is now guaranteed under all scenarios of container creation. ([#87759](https://github.com/kubernetes/kubernetes/pull/87759), [@klueska](https://github.com/klueska)) [SIG Node]
- Fixed a bug which could prevent a provider ID from ever being set for node if an error occurred determining the provider ID when the node was added. ([#87043](https://github.com/kubernetes/kubernetes/pull/87043), [@zjs](https://github.com/zjs)) [SIG Apps and Cloud Provider]
- Fixed a data race in kubelet image manager that can cause static pod workers to silently stop working. ([#88915](https://github.com/kubernetes/kubernetes/pull/88915), [@roycaihw](https://github.com/roycaihw)) [SIG Node]
- Fixed a panic in the kubelet cleaning up pod volumes ([#86277](https://github.com/kubernetes/kubernetes/pull/86277), [@tedyu](https://github.com/tedyu)) [SIG Storage]
- Fixed a regression where the kubelet would fail to update the ready status of pods. ([#84951](https://github.com/kubernetes/kubernetes/pull/84951), [@tedyu](https://github.com/tedyu)) [SIG Node]
- Fixed an issue that could cause the kubelet to incorrectly run concurrent pod reconciliation loops and crash. ([#89055](https://github.com/kubernetes/kubernetes/pull/89055), [@tedyu](https://github.com/tedyu)) [SIG Node]
- Fixed an issue that the scheduler only returns the first failure reason. ([#86022](https://github.com/kubernetes/kubernetes/pull/86022), [@Huang-Wei](https://github.com/Huang-Wei)) [SIG Scheduling]
- Fixed block CSI volume cleanup after timeouts. ([#88660](https://github.com/kubernetes/kubernetes/pull/88660), [@jsafrane](https://github.com/jsafrane)) [SIG Node and Storage]
- Fixed bug where a nonzero exit code was returned when initializing zsh completion even though zsh completion was successfully initialized ([#88165](https://github.com/kubernetes/kubernetes/pull/88165), [@brianpursley](https://github.com/brianpursley)) [SIG CLI]
- Fixed cleaning of CSI raw block volumes. ([#87978](https://github.com/kubernetes/kubernetes/pull/87978), [@jsafrane](https://github.com/jsafrane)) [SIG Storage]
- Fixed issue with addon-resizer using deprecated extensions APIs ([#85793](https://github.com/kubernetes/kubernetes/pull/85793), [@bskiba](https://github.com/bskiba)) [SIG Cluster Lifecycle and Instrumentation]
- Fixed the following 
  -  AWS Cloud Provider attempts to delete LoadBalancer security group it didn’t provision
  -  AWS Cloud Provider creates default LoadBalancer security group even if annotation [service.beta.kubernetes.io/aws-load-balancer-security-groups] is present ([#84265](https://github.com/kubernetes/kubernetes/pull/84265), [@bhagwat070919](https://github.com/bhagwat070919)) [SIG Cloud Provider]
- Fixed two scheduler metrics (pending_pods and schedule_attempts_total) not being recorded ([#87692](https://github.com/kubernetes/kubernetes/pull/87692), [@everpeace](https://github.com/everpeace)) [SIG Scheduling]
- Fixes an issue with kubelet-reported pod status on deleted/recreated pods. ([#86320](https://github.com/kubernetes/kubernetes/pull/86320), [@liggitt](https://github.com/liggitt)) [SIG Node]
- Fixes conversion error in multi-version custom resources that could cause metadata.generation to increment on no-op patches or updates of a custom resource. ([#88995](https://github.com/kubernetes/kubernetes/pull/88995), [@liggitt](https://github.com/liggitt)) [SIG API Machinery]
- Fixes issue where AAD token obtained by kubectl is incompatible with on-behalf-of flow and oidc.
  The audience claim before this fix has "spn:" prefix. After this fix, "spn:" prefix is omitted. ([#86412](https://github.com/kubernetes/kubernetes/pull/86412), [@weinong](https://github.com/weinong)) [SIG API Machinery, Auth and Cloud Provider]
- Fixes issue where you can't attach more than 15 GCE Persistent Disks to c2, n2, m1, m2 machine types. ([#88602](https://github.com/kubernetes/kubernetes/pull/88602), [@yuga711](https://github.com/yuga711)) [SIG Storage]
- Fixes kube-proxy when EndpointSlice feature gate is enabled on Windows. ([#86016](https://github.com/kubernetes/kubernetes/pull/86016), [@robscott](https://github.com/robscott)) [SIG Auth and Network]
- Fixes kubelet crash in client certificate rotation cases ([#88079](https://github.com/kubernetes/kubernetes/pull/88079), [@liggitt](https://github.com/liggitt)) [SIG API Machinery, Auth and Node]
- Fixes service account token admission error in clusters that do not run the service account token controller ([#87029](https://github.com/kubernetes/kubernetes/pull/87029), [@liggitt](https://github.com/liggitt)) [SIG Auth]
- Fixes v1.17.0 regression in --service-cluster-ip-range handling with IPv4 ranges larger than 65536 IP addresses ([#86534](https://github.com/kubernetes/kubernetes/pull/86534), [@liggitt](https://github.com/liggitt)) [SIG Network]
- Fixes wrong validation result of NetworkPolicy PolicyTypes ([#85747](https://github.com/kubernetes/kubernetes/pull/85747), [@tnqn](https://github.com/tnqn)) [SIG Network]
- For subprotocol negotiation, both client and server protocol is required now. ([#86646](https://github.com/kubernetes/kubernetes/pull/86646), [@tedyu](https://github.com/tedyu)) [SIG API Machinery and Node]
- For volumes that allow attaches across multiple nodes, attach and detach operations across different nodes are now executed in parallel. ([#88678](https://github.com/kubernetes/kubernetes/pull/88678), [@verult](https://github.com/verult)) [SIG Apps, Node and Storage]
- Garbage collector now can correctly orphan ControllerRevisions when StatefulSets are deleted with orphan propagation policy. ([#84984](https://github.com/kubernetes/kubernetes/pull/84984), [@cofyc](https://github.com/cofyc)) [SIG Apps]
- Get-kube.sh uses the gcloud's current local GCP service account for auth when the provider is GCE or GKE instead of the metadata server default ([#88383](https://github.com/kubernetes/kubernetes/pull/88383), [@BenTheElder](https://github.com/BenTheElder)) [SIG Cluster Lifecycle]
- Golang/x/net has been updated to bring in fixes for CVE-2020-9283 ([#88381](https://github.com/kubernetes/kubernetes/pull/88381), [@BenTheElder](https://github.com/BenTheElder)) [SIG API Machinery, CLI, Cloud Provider, Cluster Lifecycle and Instrumentation]
- Hide kubectl.kubernetes.io/last-applied-configuration in describe command ([#88758](https://github.com/kubernetes/kubernetes/pull/88758), [@soltysh](https://github.com/soltysh)) [SIG Auth and CLI]
- Hollow-node do not use remote CRI anymore ([#86425](https://github.com/kubernetes/kubernetes/pull/86425), [@jkaniuk](https://github.com/jkaniuk)) [SIG Node and Scalability]
- If a required flag is not provided to a command, the user will only see the required flag error message, instead of the entire usage menu. ([#86693](https://github.com/kubernetes/kubernetes/pull/86693), [@sallyom](https://github.com/sallyom)) [SIG CLI]
- If a serving certificates param specifies a name that is an IP for an SNI certificate, it will have priority for replying to server connections. ([#85308](https://github.com/kubernetes/kubernetes/pull/85308), [@deads2k](https://github.com/deads2k)) [SIG API Machinery]
- If lastTimestamp is not set use firstTimestamp when printing event ([#86557](https://github.com/kubernetes/kubernetes/pull/86557), [@soltysh](https://github.com/soltysh)) [SIG CLI]
- Improved yaml parsing performance ([#85458](https://github.com/kubernetes/kubernetes/pull/85458), [@cjcullen](https://github.com/cjcullen)) [SIG API Machinery, CLI, Cloud Provider, Cluster Lifecycle, Instrumentation and Node]
- Improves performance of the node authorizer ([#87696](https://github.com/kubernetes/kubernetes/pull/87696), [@liggitt](https://github.com/liggitt)) [SIG Auth]
- In GKE alpha clusters it will be possible to use the service annotation `cloud.google.com/network-tier: Standard` ([#88487](https://github.com/kubernetes/kubernetes/pull/88487), [@zioproto](https://github.com/zioproto)) [SIG Cloud Provider]
- Includes FSType when describing CSI persistent volumes. ([#85293](https://github.com/kubernetes/kubernetes/pull/85293), [@huffmanca](https://github.com/huffmanca)) [SIG CLI and Storage]
- Iptables/userspace proxy: improve performance by getting local addresses only once per sync loop, instead of for every external IP ([#85617](https://github.com/kubernetes/kubernetes/pull/85617), [@andrewsykim](https://github.com/andrewsykim)) [SIG API Machinery, CLI, Cloud Provider, Cluster Lifecycle, Instrumentation and Network]
- Ipvs: only attempt setting of sysctlconnreuse on supported kernels ([#88541](https://github.com/kubernetes/kubernetes/pull/88541), [@cmluciano](https://github.com/cmluciano)) [SIG Network]
- Kube-aggregator: always sets unavailableGauge metric to reflect the current state of a service. ([#87778](https://github.com/kubernetes/kubernetes/pull/87778), [@p0lyn0mial](https://github.com/p0lyn0mial)) [SIG API Machinery]
- Kube-apiserver: Fixes a bug that hidden metrics can not be enabled by the command-line option `--show-hidden-metrics-for-version`. ([#85444](https://github.com/kubernetes/kubernetes/pull/85444), [@RainbowMango](https://github.com/RainbowMango)) [SIG API Machinery, Cluster Lifecycle and Instrumentation]
- Kube-apiserver: fixed a conflict error encountered attempting to delete a pod with gracePeriodSeconds=0 and a resourceVersion precondition ([#85516](https://github.com/kubernetes/kubernetes/pull/85516), [@michaelgugino](https://github.com/michaelgugino)) [SIG API Machinery]
- Kube-proxy no longer modifies shared EndpointSlices. ([#86092](https://github.com/kubernetes/kubernetes/pull/86092), [@robscott](https://github.com/robscott)) [SIG Network]
- Kube-proxy: on dual-stack mode, if it is not able to get the IP Family of an endpoint, logs it with level InfoV(4) instead of Warning, avoiding flooding the logs for endpoints without addresses ([#88934](https://github.com/kubernetes/kubernetes/pull/88934), [@aojea](https://github.com/aojea)) [SIG Network]
- Kubeadm allows to configure single-stack clusters if dual-stack is enabled ([#87453](https://github.com/kubernetes/kubernetes/pull/87453), [@aojea](https://github.com/aojea)) [SIG API Machinery, Cluster Lifecycle and Network]
- Kubeadm now includes CoreDNS version 1.6.7 ([#86260](https://github.com/kubernetes/kubernetes/pull/86260), [@rajansandeep](https://github.com/rajansandeep)) [SIG Cluster Lifecycle]
- Kubeadm upgrades always persist the etcd backup for stacked ([#86861](https://github.com/kubernetes/kubernetes/pull/86861), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle]
- Kubeadm:  'kubeadm alpha kubelet config download' has been removed, please use 'kubeadm upgrade node phase kubelet-config' instead ([#87944](https://github.com/kubernetes/kubernetes/pull/87944), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle]
- Kubeadm: Forward cluster name to the controller-manager arguments ([#85817](https://github.com/kubernetes/kubernetes/pull/85817), [@ereslibre](https://github.com/ereslibre)) [SIG Cluster Lifecycle]
- Kubeadm: add support for the "ci/k8s-master" version label as a replacement for "ci-cross/*", which no longer exists. ([#86609](https://github.com/kubernetes/kubernetes/pull/86609), [@Pensu](https://github.com/Pensu)) [SIG Cluster Lifecycle]
- Kubeadm: apply further improvements to the tentative support for concurrent etcd member join. Fixes a bug where multiple members can receive the same hostname. Increase the etcd client dial timeout and retry timeout for add/remove/... operations. ([#87505](https://github.com/kubernetes/kubernetes/pull/87505), [@neolit123](https://github.com/neolit123)) [SIG Cluster Lifecycle]
- Kubeadm: don't write the kubelet environment file on "upgrade apply" ([#85412](https://github.com/kubernetes/kubernetes/pull/85412), [@boluisa](https://github.com/boluisa)) [SIG Cluster Lifecycle]
- Kubeadm: fix potential panic when executing "kubeadm reset" with a corrupted kubelet.conf file ([#86216](https://github.com/kubernetes/kubernetes/pull/86216), [@neolit123](https://github.com/neolit123)) [SIG Cluster Lifecycle]
- Kubeadm: fix the bug that 'kubeadm upgrade' hangs in single node cluster ([#88434](https://github.com/kubernetes/kubernetes/pull/88434), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle]
- Kubeadm: make sure images are pre-pulled even if a tag did not change but their contents changed ([#85603](https://github.com/kubernetes/kubernetes/pull/85603), [@bart0sh](https://github.com/bart0sh)) [SIG Cluster Lifecycle]
- Kubeadm: remove 'kubeadm upgrade node config' command since it was deprecated in v1.15, please use 'kubeadm upgrade node phase kubelet-config' instead ([#87975](https://github.com/kubernetes/kubernetes/pull/87975), [@SataQiu](https://github.com/SataQiu)) [SIG Cluster Lifecycle]
- Kubeadm: remove the deprecated CoreDNS feature-gate. It was set to "true" since v1.11 when the feature went GA. In v1.13 it was marked as deprecated and hidden from the CLI. ([#87400](https://github.com/kubernetes/kubernetes/pull/87400), [@neolit123](https://github.com/neolit123)) [SIG Cluster Lifecycle]
- Kubeadm: retry `kubeadm-config` ConfigMap creation or mutation if the apiserver is not responding. This will improve resiliency when joining new control plane nodes. ([#85763](https://github.com/kubernetes/kubernetes/pull/85763), [@ereslibre](https://github.com/ereslibre)) [SIG Cluster Lifecycle]
- Kubeadm: tolerate whitespace when validating certificate authority PEM data in kubeconfig files ([#86705](https://github.com/kubernetes/kubernetes/pull/86705), [@neolit123](https://github.com/neolit123)) [SIG Cluster Lifecycle]
- Kubeadm: use bind-address option to configure the kube-controller-manager and kube-scheduler http probes ([#86493](https://github.com/kubernetes/kubernetes/pull/86493), [@aojea](https://github.com/aojea)) [SIG Cluster Lifecycle]
- Kubeadm: uses the apiserver AdvertiseAddress IP family to choose the etcd endpoint IP family for non external etcd clusters ([#85745](https://github.com/kubernetes/kubernetes/pull/85745), [@aojea](https://github.com/aojea)) [SIG Cluster Lifecycle]
- Kubectl cluster-info dump --output-directory=xxx now generates files with an extension depending on the output format. ([#82070](https://github.com/kubernetes/kubernetes/pull/82070), [@olivierlemasle](https://github.com/olivierlemasle)) [SIG CLI]
- Kubectl describe <type> and kubectl top pod will return a message saying "No resources found" or "No resources found in <namespace> namespace" if there are no results to display. ([#87527](https://github.com/kubernetes/kubernetes/pull/87527), [@brianpursley](https://github.com/brianpursley)) [SIG CLI]
- Kubectl drain node --dry-run will list pods that would be evicted or deleted ([#82660](https://github.com/kubernetes/kubernetes/pull/82660), [@sallyom](https://github.com/sallyom)) [SIG CLI]
- Kubectl set resources will no longer return an error if passed an empty change for a resource. 
  kubectl set subject will no longer return an error if passed an empty change for a resource. ([#85490](https://github.com/kubernetes/kubernetes/pull/85490), [@sallyom](https://github.com/sallyom)) [SIG CLI]
- Kubelet metrics gathered through metrics-server or prometheus should no longer timeout for Windows nodes running more than 3 pods. ([#87730](https://github.com/kubernetes/kubernetes/pull/87730), [@marosset](https://github.com/marosset)) [SIG Node, Testing and Windows]
- Kubelet metrics have been changed to buckets. 
  For example the exec/{podNamespace}/{podID}/{containerName} is now just exec. ([#87913](https://github.com/kubernetes/kubernetes/pull/87913), [@cheftako](https://github.com/cheftako)) [SIG Node]
- Kubelet: fix the bug that kubelet help information can not show the right type of flags ([#88515](https://github.com/kubernetes/kubernetes/pull/88515), [@SataQiu](https://github.com/SataQiu)) [SIG Docs and Node]
- Kubelets perform fewer unnecessary pod status update operations on the API server. ([#88591](https://github.com/kubernetes/kubernetes/pull/88591), [@smarterclayton](https://github.com/smarterclayton)) [SIG Node and Scalability]
- Kubernetes will try to acquire the iptables lock every 100 msec during 5 seconds instead of every second. This specially useful for environments using kube-proxy in iptables mode with a high churn rate of services. ([#85771](https://github.com/kubernetes/kubernetes/pull/85771), [@aojea](https://github.com/aojea)) [SIG Network]
- Limit number of instances in a single update to GCE target pool to 1000. ([#87881](https://github.com/kubernetes/kubernetes/pull/87881), [@wojtek-t](https://github.com/wojtek-t)) [SIG Cloud Provider, Network and Scalability]
- Make Azure clients only retry on specified HTTP status codes ([#88017](https://github.com/kubernetes/kubernetes/pull/88017), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Make error message and service event message more clear ([#86078](https://github.com/kubernetes/kubernetes/pull/86078), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Minimize AWS NLB health check timeout when externalTrafficPolicy set to Local ([#73363](https://github.com/kubernetes/kubernetes/pull/73363), [@kellycampbell](https://github.com/kellycampbell)) [SIG Cloud Provider]
- Optimize kubectl version help info ([#88313](https://github.com/kubernetes/kubernetes/pull/88313), [@zhouya0](https://github.com/zhouya0)) [SIG CLI]
- OwnerReferences []OwnerReferencematches 
  so nil len(OwnerReferences) ==0 
  
  change:
   if lease.OwnerReferences == nil || len(lease.OwnerReferences) == 0 ==>if len(lease.OwnerReferences) == 0 ([#85789](https://github.com/kubernetes/kubernetes/pull/85789), [@ZP-AlwaysWin](https://github.com/ZP-AlwaysWin)) [SIG Node]
- Pause image contains "Architecture" in non-amd64 images ([#87954](https://github.com/kubernetes/kubernetes/pull/87954), [@BenTheElder](https://github.com/BenTheElder)) [SIG Release]
- Pause image upgraded to 3.2 in kubelet and kubeadm. ([#88173](https://github.com/kubernetes/kubernetes/pull/88173), [@BenTheElder](https://github.com/BenTheElder)) [SIG CLI, Cluster Lifecycle, Node and Testing]
- Plugin/PluginConfig and Policy APIs are mutually exclusive when running the scheduler ([#88864](https://github.com/kubernetes/kubernetes/pull/88864), [@alculquicondor](https://github.com/alculquicondor)) [SIG Scheduling]
- Pods that are considered for preemption and haven't started don't produce an error log. ([#87900](https://github.com/kubernetes/kubernetes/pull/87900), [@alculquicondor](https://github.com/alculquicondor)) [SIG Scheduling]
- Prevent error message from being displayed when running kubectl plugin list and your path includes an empty string ([#87633](https://github.com/kubernetes/kubernetes/pull/87633), [@brianpursley](https://github.com/brianpursley)) [SIG CLI]
- Remove `FilteredNodesStatuses` argument from `PreScore`'s interface. ([#88189](https://github.com/kubernetes/kubernetes/pull/88189), [@skilxn-go](https://github.com/skilxn-go)) [SIG Scheduling and Testing]
- Removed the 'client' label from apiserver_request_total. ([#87669](https://github.com/kubernetes/kubernetes/pull/87669), [@logicalhan](https://github.com/logicalhan)) [SIG API Machinery and Instrumentation]
- Removes the deprecated command `kubectl rolling-update` ([#88057](https://github.com/kubernetes/kubernetes/pull/88057), [@julianvmodesto](https://github.com/julianvmodesto)) [SIG Architecture, CLI and Testing]
- Resolved a performance issue in the node authorizer index maintenance. ([#87693](https://github.com/kubernetes/kubernetes/pull/87693), [@liggitt](https://github.com/liggitt)) [SIG Auth]
- Resolved regression in admission, authentication, and authorization webhook performance in v1.17.0-rc.1 ([#85810](https://github.com/kubernetes/kubernetes/pull/85810), [@liggitt](https://github.com/liggitt)) [SIG API Machinery and Testing]
- Resolves performance regression in `kubectl get all` and in client-go discovery clients constructed using `NewDiscoveryClientForConfig` or `NewDiscoveryClientForConfigOrDie`. ([#86168](https://github.com/kubernetes/kubernetes/pull/86168), [@liggitt](https://github.com/liggitt)) [SIG API Machinery]
- Reverted a kubectl azure auth module change where oidc claim spn: prefix was omitted resulting a breaking behavior with existing Azure AD OIDC enabled api-server ([#87507](https://github.com/kubernetes/kubernetes/pull/87507), [@weinong](https://github.com/weinong)) [SIG API Machinery, Auth and Cloud Provider]
- Shared informers are now more reliable in the face of network disruption. ([#86015](https://github.com/kubernetes/kubernetes/pull/86015), [@squeed](https://github.com/squeed)) [SIG API Machinery]
- Specifying PluginConfig for the same plugin more than once fails scheduler startup.
  
  Specifying extenders and configuring .ignoredResources for the NodeResourcesFit plugin fails ([#88870](https://github.com/kubernetes/kubernetes/pull/88870), [@alculquicondor](https://github.com/alculquicondor)) [SIG Scheduling]
- Support TLS Server Name overrides in kubeconfig file and via --tls-server-name in kubectl ([#88769](https://github.com/kubernetes/kubernetes/pull/88769), [@deads2k](https://github.com/deads2k)) [SIG API Machinery, Auth and CLI]
- Terminating a restartPolicy=Never pod no longer has a chance to report the pod succeeded when it actually failed. ([#88440](https://github.com/kubernetes/kubernetes/pull/88440), [@smarterclayton](https://github.com/smarterclayton)) [SIG Node and Testing]
- The CSR signing cert/key pairs will be reloaded from disk like the kube-apiserver cert/key pairs ([#86816](https://github.com/kubernetes/kubernetes/pull/86816), [@deads2k](https://github.com/deads2k)) [SIG API Machinery, Apps and Auth]
- The EventRecorder from k8s.io/client-go/tools/events will now create events in the default namespace (instead of kube-system) when the related object does not have it set. ([#88815](https://github.com/kubernetes/kubernetes/pull/88815), [@enj](https://github.com/enj)) [SIG API Machinery]
- The audit event sourceIPs list will now always end with the IP that sent the request directly to the API server. ([#87167](https://github.com/kubernetes/kubernetes/pull/87167), [@tallclair](https://github.com/tallclair)) [SIG API Machinery and Auth]
- The following deprecated metrics are removed, please convert to the corresponding metrics:
  1. The following replacement metrics are available from v1.14.0:
  - `rest_client_request_latency_seconds` -> `rest_client_request_duration_seconds`
  - `scheduler_scheduling_latency_seconds` -> `scheduler_scheduling_duration_seconds `
  - `docker_operations` -> `docker_operations_total`
  - `docker_operations_latency_microseconds` -> `docker_operations_duration_seconds`
  - `docker_operations_errors` -> `docker_operations_errors_total`
  - `docker_operations_timeout` -> `docker_operations_timeout_total`
  - `network_plugin_operations_latency_microseconds` -> `network_plugin_operations_duration_seconds`
  - `kubelet_pod_worker_latency_microseconds` -> `kubelet_pod_worker_duration_seconds`
  - `kubelet_pod_start_latency_microseconds` -> `kubelet_pod_start_duration_seconds`
  - `kubelet_cgroup_manager_latency_microseconds` -> `kubelet_cgroup_manager_duration_seconds`
  - `kubelet_pod_worker_start_latency_microseconds` -> `kubelet_pod_worker_start_duration_seconds`
  - `kubelet_pleg_relist_latency_microseconds` -> `kubelet_pleg_relist_duration_seconds`
  - `kubelet_pleg_relist_interval_microseconds` -> `kubelet_pleg_relist_interval_seconds`
  - `kubelet_eviction_stats_age_microseconds` -> `kubelet_eviction_stats_age_seconds`
  - `kubelet_runtime_operations` -> `kubelet_runtime_operations_total`
  - `kubelet_runtime_operations_latency_microseconds` -> `kubelet_runtime_operations_duration_seconds`
  - `kubelet_runtime_operations_errors` -> `kubelet_runtime_operations_errors_total`
  - `kubelet_device_plugin_registration_count` -> `kubelet_device_plugin_registration_total`
  - `kubelet_device_plugin_alloc_latency_microseconds` -> `kubelet_device_plugin_alloc_duration_seconds`
  - `scheduler_e2e_scheduling_latency_microseconds` -> `scheduler_e2e_scheduling_duration_seconds`
  - `scheduler_scheduling_algorithm_latency_microseconds` -> `scheduler_scheduling_algorithm_duration_seconds`
  - `scheduler_scheduling_algorithm_predicate_evaluation` -> `scheduler_scheduling_algorithm_predicate_evaluation_seconds`
  - `scheduler_scheduling_algorithm_priority_evaluation` -> `scheduler_scheduling_algorithm_priority_evaluation_seconds`
  - `scheduler_scheduling_algorithm_preemption_evaluation` -> `scheduler_scheduling_algorithm_preemption_evaluation_seconds`
  - `scheduler_binding_latency_microseconds` -> `scheduler_binding_duration_seconds`
  - `kubeproxy_sync_proxy_rules_latency_microseconds` -> `kubeproxy_sync_proxy_rules_duration_seconds`
  - `apiserver_request_latencies` -> `apiserver_request_duration_seconds`
  - `apiserver_dropped_requests` -> `apiserver_dropped_requests_total`
  - `etcd_request_latencies_summary` -> `etcd_request_duration_seconds`
  - `apiserver_storage_transformation_latencies_microseconds ` -> `apiserver_storage_transformation_duration_seconds`
  - `apiserver_storage_data_key_generation_latencies_microseconds` -> `apiserver_storage_data_key_generation_duration_seconds`
  - `apiserver_request_count` -> `apiserver_request_total`
  - `apiserver_request_latencies_summary`
  2. The following replacement metrics are available from v1.15.0:
  - `apiserver_storage_transformation_failures_total` -> `apiserver_storage_transformation_operations_total` ([#76496](https://github.com/kubernetes/kubernetes/pull/76496), [@danielqsj](https://github.com/danielqsj)) [SIG API Machinery, Cluster Lifecycle, Instrumentation, Network, Node and Scheduling]
- The following features are unconditionally enabled and the corresponding `--feature-gates` flags have been removed: `PodPriority`, `TaintNodesByCondition`, `ResourceQuotaScopeSelectors` and `ScheduleDaemonSetPods` ([#86210](https://github.com/kubernetes/kubernetes/pull/86210), [@draveness](https://github.com/draveness)) [SIG Apps and Scheduling]
- The sample-apiserver aggregated conformance test has updated to use the Kubernetes v1.17.0 sample apiserver ([#84735](https://github.com/kubernetes/kubernetes/pull/84735), [@liggitt](https://github.com/liggitt)) [SIG API Machinery, Architecture, CLI and Testing]
- To reduce chances of throttling, VM cache is set to nil when Azure node provisioning state is deleting ([#87635](https://github.com/kubernetes/kubernetes/pull/87635), [@feiskyer](https://github.com/feiskyer)) [SIG Cloud Provider]
- Update Cluster Autoscaler to 1.17.0; changelog: https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.17.0 ([#85610](https://github.com/kubernetes/kubernetes/pull/85610), [@losipiuk](https://github.com/losipiuk)) [SIG Autoscaling and Cluster Lifecycle]
- Update Cluster Autoscaler to 1.18.0; changelog: https://github.com/kubernetes/autoscaler/releases/tag/cluster-autoscaler-1.18.0 ([#89095](https://github.com/kubernetes/kubernetes/pull/89095), [@losipiuk](https://github.com/losipiuk)) [SIG Autoscaling and Cluster Lifecycle]
- Update go-winio module version from 0.4.11 to 0.4.14 ([#85739](https://github.com/kubernetes/kubernetes/pull/85739), [@wawa0210](https://github.com/wawa0210)) [SIG Windows]
- Update to use golang 1.13.8 ([#87648](https://github.com/kubernetes/kubernetes/pull/87648), [@ialidzhikov](https://github.com/ialidzhikov)) [SIG Release and Testing]
- Use context to check client closed instead of http.CloseNotifier in processing watch request which will reduce 1 goroutine for each request if proto is HTTP/2.x . ([#85408](https://github.com/kubernetes/kubernetes/pull/85408), [@answer1991](https://github.com/answer1991)) [SIG API Machinery]
- VMSS cache is added so that less chances of VMSS GET throttling ([#85885](https://github.com/kubernetes/kubernetes/pull/85885), [@nilo19](https://github.com/nilo19)) [SIG Cloud Provider]
- Validate kube-proxy flags --ipvs-tcp-timeout, --ipvs-tcpfin-timeout, --ipvs-udp-timeout ([#88657](https://github.com/kubernetes/kubernetes/pull/88657), [@chendotjs](https://github.com/chendotjs)) [SIG Network]
- Wait for all CRDs to show up in discovery endpoint before reporting readiness. ([#89145](https://github.com/kubernetes/kubernetes/pull/89145), [@sttts](https://github.com/sttts)) [SIG API Machinery]
- Wait for kubelet & kube-proxy to be ready on Windows node within 10s ([#85228](https://github.com/kubernetes/kubernetes/pull/85228), [@YangLu1031](https://github.com/YangLu1031)) [SIG Cluster Lifecycle]
- `(*"k8s.io/client-go/rest".Request).{Do,DoRaw,Stream,Watch}` now require callers to pass a `context.Context` as an argument. The context is used for timeout and cancellation signaling and to pass supplementary information to round trippers in the wrapped transport chain. If you don't need any of this functionality, it is sufficient to pass a context created with `context.Background()` to these functions. The `(*"k8s.io/client-go/rest".Request).Context` method is removed now that all methods that execute a request accept a context directly. ([#87597](https://github.com/kubernetes/kubernetes/pull/87597), [@mikedanese](https://github.com/mikedanese)) [SIG API Machinery, Apps, Autoscaling, CLI, Cloud Provider, Cluster Lifecycle, Instrumentation, Network, Node, Scheduling, Storage and Testing]
- `kubectl apply  -f <file> --prune -n <namespace>` should prune all resources not defined in the file in the cli specified namespace. ([#85613](https://github.com/kubernetes/kubernetes/pull/85613), [@MartinKaburu](https://github.com/MartinKaburu)) [SIG CLI]
- `kubectl create clusterrolebinding` creates rbac.authorization.k8s.io/v1 object ([#85889](https://github.com/kubernetes/kubernetes/pull/85889), [@oke-py](https://github.com/oke-py)) [SIG CLI]
- `kubectl diff` now returns 1 only on diff finding changes, and >1 on kubectl errors. The "exit status code 1" message as also been muted. ([#87437](https://github.com/kubernetes/kubernetes/pull/87437), [@apelisse](https://github.com/apelisse)) [SIG CLI and Testing]