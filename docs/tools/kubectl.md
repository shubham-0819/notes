# kubectl Cheatsheet

UPPERCASE words are placeholders (`POD`, `NS`, `DEPLOY`).

## Contents

- [Context and namespace](#context-and-namespace)
- [Inspect resources](#inspect-resources)
- [Logs and debugging](#logs-and-debugging)
- [Exec, port-forward, copy](#exec-port-forward-copy)
- [Create and apply](#create-and-apply)
- [Edit, scale, roll out](#edit-scale-roll-out)
- [Delete](#delete)
- [Output and querying](#output-and-querying)
- [Nodes and cluster](#nodes-and-cluster)
- [Access and permissions](#access-and-permissions)
- [Tips and tricks](#tips-and-tricks)

---

## Context and namespace

```bash
kubectl config get-contexts                               # list contexts
kubectl config use-context CTX                            # switch cluster
kubectl config current-context                            # where am I?
kubectl config set-context --current --namespace=NS       # set default namespace
kubectl get pods -n NS                                    # one-off namespace
kubectl get pods -A                                       # all namespaces
```

## Inspect resources

```bash
kubectl get pods,svc,deploy                               # several types at once
kubectl get pods -o wide                                  # adds node and IP
kubectl get pods -w                                       # watch for changes
kubectl get pods -l app=web                               # filter by label
kubectl get pods --show-labels                            # show labels
kubectl get pods --field-selector status.phase=Running   # filter by field
kubectl describe pod POD                                  # details plus events
kubectl get all                                           # common workload types only, not everything
kubectl api-resources                                     # every resource kind and short name
kubectl explain pod.spec.containers                       # built-in schema docs
```

## Logs and debugging

```bash
kubectl logs POD                                          # container logs
kubectl logs -f POD                                       # follow
kubectl logs POD -c CONTAINER                             # pick a container
kubectl logs POD --previous                               # logs from the crashed instance
kubectl logs -l app=web --tail=50 --since=10m             # by label, recent only
kubectl logs deploy/DEPLOY --all-containers               # via the deployment
kubectl get events --sort-by=.lastTimestamp               # recent cluster events
kubectl top pod --containers                              # CPU/memory (needs metrics-server)
kubectl debug POD -it --image=busybox --target=CONTAINER  # ephemeral debug container
kubectl debug node/NODE -it --image=busybox               # shell on a node
```

## Exec, port-forward, copy

```bash
kubectl exec -it POD -- sh                                # shell in a pod
kubectl exec POD -c CONTAINER -- env                      # run one command
kubectl port-forward pod/POD 8080:80                      # local 8080 -> pod 80
kubectl port-forward svc/SVC 8080:80                      # forward to a service
kubectl cp POD:/path/file ./file                          # copy out (needs tar in image)
kubectl run tmp --rm -it --image=busybox -- sh            # throwaway pod
```

## Create and apply

```bash
kubectl apply -f manifest.yaml                            # create or update
kubectl apply -f dir/ -R                                  # whole folder, recursive
kubectl apply -k overlays/prod                            # kustomize
kubectl diff -f manifest.yaml                             # preview changes
kubectl apply -f manifest.yaml --dry-run=server           # validate against the API server
kubectl create deploy web --image=nginx --dry-run=client -o yaml   # generate YAML scaffolding
kubectl create secret generic S --from-literal=k=v        # quick secret
kubectl create configmap C --from-file=app.conf           # quick configmap
```

## Edit, scale, roll out

```bash
kubectl edit deploy DEPLOY                                # edit live in $EDITOR
kubectl scale deploy DEPLOY --replicas=3                  # scale
kubectl set image deploy/DEPLOY app=img:v2                # change an image
kubectl rollout status deploy/DEPLOY                      # wait for rollout
kubectl rollout history deploy/DEPLOY                     # revisions
kubectl rollout undo deploy/DEPLOY                        # roll back
kubectl rollout restart deploy/DEPLOY                     # rolling restart, no YAML change
kubectl patch deploy DEPLOY -p '{"spec":{"replicas":2}}'  # inline patch
kubectl label pod POD env=dev --overwrite                 # set label
kubectl annotate pod POD note="x"                         # set annotation
```

## Delete

```bash
kubectl delete -f manifest.yaml                           # delete what a file defines
kubectl delete pod POD                                    # controller will recreate it
kubectl delete pod -l app=web                             # by label
kubectl delete pod POD --grace-period=0 --force           # last resort for stuck pods
kubectl delete pods --field-selector status.phase=Failed  # clean up failed pods
```

## Output and querying

```bash
kubectl get pod POD -o yaml                               # full object
kubectl get pods -o name                                  # names only, good for scripts
kubectl get pods -o jsonpath='{.items[*].metadata.name}'  # extract fields
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.phase}{"\n"}{end}'   # loop over items
kubectl get pods -o custom-columns=NAME:.metadata.name,NODE:.spec.nodeName   # custom table
kubectl get pods --sort-by=.status.startTime              # sort
kubectl get pods -o json | jq '.items[].spec.containers[].image'   # jq for anything complex
kubectl get secret S -o jsonpath='{.data.key}' | base64 -d         # decode a secret value
```

## Nodes and cluster

```bash
kubectl get nodes -o wide                                 # node list
kubectl top nodes                                         # node usage
kubectl cordon NODE                                       # stop scheduling here
kubectl drain NODE --ignore-daemonsets --delete-emptydir-data   # evict pods for maintenance
kubectl uncordon NODE                                     # resume scheduling
kubectl taint nodes NODE key=val:NoSchedule               # add taint
kubectl cluster-info                                      # API server and DNS endpoints
```

## Access and permissions

```bash
kubectl auth can-i create pods -n NS                      # check your access
kubectl auth can-i --list                                 # everything you may do
kubectl auth can-i get secrets --as=system:serviceaccount:NS:SA   # test as a service account
kubectl get rolebindings,clusterrolebindings -A           # who has what
```

## Tips and tricks

- **Alias and completion:** `alias k=kubectl`, then `source <(kubectl completion bash)` (or `zsh`) and `complete -o default -F __start_kubectl k`.
- **Short names:** `po`, `svc`, `deploy`, `cm`, `ns`, `pvc`, `sa`, `ing`. Run `kubectl api-resources` for the full list.
- **Generate YAML instead of writing it:** add `--dry-run=client -o yaml` to `create` or `run`, redirect to a file, then edit.
- **Diff before apply:** run `kubectl diff -f` before every `apply`, especially against prod.
- **Pod stuck?** Read `describe` events first (scheduling, image pulls, probes), then `logs --previous` for CrashLoopBackOff.
- **Skip the pod hash:** use `deploy/NAME` or `svc/NAME` in `logs`, `exec`, and `port-forward`.
- **Restart cleanly:** prefer `kubectl rollout restart` over deleting pods by hand; it respects rollout strategy and PDBs.
- **Look up fields offline:** `kubectl explain RESOURCE.field --recursive`.
- **Multi-cluster work:** use `kubectx` and `kubens`, or set `KUBECONFIG` per shell. Show the current context in your prompt.
- **Better tooling:** `stern` tails logs across multiple pods; `k9s` is a terminal UI for browsing a cluster.
- **Force delete caveat:** `--force --grace-period=0` skips graceful shutdown and can leave orphaned processes. Use it only when a pod is truly stuck.
- **Version skew:** keep the `kubectl` client within one minor version of the server (`kubectl version`).