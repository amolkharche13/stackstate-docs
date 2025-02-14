---
description: SUSE Observability Self-hosted
---

# Un-install SUSE Observability

## Overview

1. [Uninstall the Helm cart](uninstall.md#un-install-the-helm-chart)
2. [Remove remaining resources](uninstall.md#remove-remaining-resources)
3. [Remove manually created resources](uninstall.md#remove-manually-created-resources)

## Un-install the Helm chart

{% hint style="info" %}
Un-installing the helm chart will preserve all data because helm will not remove the Persistent Volume Claims nor the namespace. To remove those as well also [remove the remaining resources](uninstall.md#remove-remaining-resources).
{% endhint %}

To un-install SUSE Observability the first action is to run the `helm uninstall` command. This command will remove all resources created by the `helm upgrade --install` command.

Uninstall the `suse-observability` release from the `suse-observability` namespace like this, replace the namespace or release name with any custom names used during installation:

```
helm uninstall --namespace suse-observability suse-observability
```

The command will return almost immediately but shutting down all the pods and removing all other resources can take a while. Check if all pods are gone with:
```
kubectl get pods --namespace suse-observability
```

If you want to re-install SUSE Observability later and have the old data still available this is all, for a full uninstall continue with the next 2 sections.

## Remove remaining resources

{% hint style="warning" %}
Removing the Persistent Volume Claims and/or the namespace will result in all data being lost that was stored in SUSE Observability.
{% endhint %}

To remove the namespace and with that, the Persistent Volume Claims and their linked Persistent Volumes simply remove the entire namespace:

```
kubectl delete namespace suse-observability
```

When the command returns the namespace and all volumes will have been removed.

To only remove the Persistent Volume Claims (PVCs) and keep the namespace run:

```
kubectl delete pvc --all -n suse-observability
```

On OpenShift the Helm chart also created a security context constraint (SCC). It is not cleaned up automatically by Helm but instead needs to be manually removed:

```
# The scc is always named suse-observability-<namespace>
oc delete scc suse-observability-suse-observability
```

## Remove manually created resources

{% hint style="info" %}
Even if you intend to re-install SUSE Observability on the same cluster but in a different namespace these can be removed. The resources contain references to the SUSE Observability namespace.
{% endhint %}

As described in the [required permissions](required_permissions.md#manually-create-cluster-wide-resources) it might have been necessary that your cluster admin created some resources manually. These resources can now be removed again, but that also is a manual task that requires admin permission.

Delete the cluster role and the cluster role bindings that have been created like this:

```
kubectl delete cluster-role suse-observability-authorization
kubectl delete cluster-role-binding suse-observability-authorization
kubectl delete cluster-role-binding suse-observability-authentication
```
