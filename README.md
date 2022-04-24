### cit965 k8s course
learning k8s by prs and issues

### PR
- [#17 facilitate better client side UX](https://github.com/kubernetes/kubernetes/pull/17/files)
- [#23 Add test to kubelet, coverage up to 37%](https://github.com/kubernetes/kubernetes/pull/23)
- [#26 Extend the CLI output to allow JSON, YAML and Human Readable output](https://github.com/kubernetes/kubernetes/pull/26/files)
- [#27 Part one of the grand rename: Task -> Pod](https://github.com/kubernetes/kubernetes/pull/27/files)
- [#36 Expand testing of the util package. Now 70%](https://github.com/kubernetes/kubernetes/pull/36/files)
--- 
- [#41 Make it clear that Kubernetes can run anywhere but that the initial scri](https://github.com/kubernetes/kubernetes/pull/41/files)
- [#69 Fix the first fit scheduler to randomize](https://github.com/kubernetes/kubernetes/pull/69/files)
- [#72 Localkube](https://github.com/kubernetes/kubernetes/pull/72/files)
- [#74 cloudcfg: resize <name> <replicas> command ](https://github.com/kubernetes/kubernetes/pull/74/files)
- [#77 Expand unit tests, coverage now to 56.9% ](https://github.com/kubernetes/kubernetes/pull/77/files)
---
13
- [#79 Parse settings client-side (addresses #67) ](https://github.com/kubernetes/kubernetes/pull/79/files)
- [#98 Only manage containers with '--' in the name. Addresses #4 ](https://github.com/kubernetes/kubernetes/pull/98/files)
- [#99 Improve e2e (improve #3) ](https://github.com/kubernetes/kubernetes/pull/99/files)
- [#100 Add nice(r) error message on api server panic. Fix nil ptr derefs. ](https://github.com/kubernetes/kubernetes/pull/100/files)
---
14
- [Fix error recovery. #107](https://github.com/kubernetes/kubernetes/pull/107/files)
- [Add udp support, and unit tests to match. Closes #96 #117](https://github.com/kubernetes/kubernetes/pull/117/files)
- [Refactor apiserver #121](https://github.com/kubernetes/kubernetes/pull/121/files)
- [Add load balancing support to services. #135](https://github.com/kubernetes/kubernetes/pull/135/files)
- [Normalize etcd_registry's storage & error handling #138](https://github.com/kubernetes/kubernetes/pull/138/files)
- [Build Kubernetes in Docker #141](https://github.com/kubernetes/kubernetes/pull/141/files)
---
15
- [Refactor controller manager.#144](https://github.com/kubernetes/kubernetes/pull/144/files)
- [Part #1 of synchronous requests: Add channels and a mechanism for waiting #166](https://github.com/kubernetes/kubernetes/pull/166)
- [Wire in the pod cache. Just used for List for now. #171](https://github.com/kubernetes/kubernetes/pull/171)
- [Letting kubelet retrieve container stats from cAdvisor #174](https://github.com/kubernetes/kubernetes/pull/174)
- [Add config dir support to kubelet #173](https://github.com/kubernetes/kubernetes/pull/173)
---
16
- [Adding support for external mounts #178](https://github.com/kubernetes/kubernetes/pull/178)
- [Build runtime Docker images. #179](https://github.com/kubernetes/kubernetes/pull/179/files)
- [Make minions first class citizens #180](https://github.com/kubernetes/kubernetes/pull/180)
- [Update IP assignment to be per-pod, not per-container](https://github.com/kubernetes/kubernetes/pull/182)
- [pkg/client: refactor tests #183](https://github.com/kubernetes/kubernetes/pull/183)
---
17
- [Make api able to marshal its types correctly#196](https://github.com/kubernetes/kubernetes/pull/196)
- [Add unit test for cloudcfg.LoadAuthInfo #197](https://github.com/kubernetes/kubernetes/pull/197)
- [Add a 'k8s' prefix to docker containers that we manage #200](https://github.com/kubernetes/kubernetes/pull/200/files)
- [Cleanup handling of config channels in RunSyncLoop #201](https://github.com/kubernetes/kubernetes/pull/201/files)
- [Small client refactor; interpret 202 responses #205](https://github.com/kubernetes/kubernetes/pull/205)
- [Add script to verify all boilerplate; add line to make travis run it. #210](https://github.com/kubernetes/kubernetes/pull/210)
18
- [Give api server operation tracking ability #249](https://github.com/kubernetes/kubernetes/pull/249/commits)
- [Rename cloudcfg to kubecfg #252](https://github.com/kubernetes/kubernetes/pull/252)
- [Use net.JoinHostPort #258](https://github.com/kubernetes/kubernetes/pull/258/files)
---
21
- [Solved data races in pkg/registry #306](https://github.com/kubernetes/kubernetes/pull/306)
- [All PUTs now atomic #307](https://github.com/kubernetes/kubernetes/pull/307)
- [Break the dep from util -> api #308](https://github.com/kubernetes/kubernetes/pull/308)
- [fix data races in controller #309](https://github.com/kubernetes/kubernetes/pull/309)
- [Fileserver #313](https://github.com/kubernetes/kubernetes/pull/313)
- [Fix interface{} in api/types #318](https://github.com/kubernetes/kubernetes/pull/318)
- [Initial add of an environment variable for the kubernetes master. #319](https://github.com/kubernetes/kubernetes/pull/319)
- [Make each pod synchronization in the kubelet an independent thread. #320](https://github.com/kubernetes/kubernetes/pull/320)