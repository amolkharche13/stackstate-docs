---
description: SUSE Observability Self-hosted
---

# Scopes

## How do scopes work?

The scope is an [STQL query](../../../develop/reference/k8sTs-stql_reference.md) that's added as a prefix to every query executed in SUSE Observability. Whenever a user wants to select a view or pass a query in SUSE Observability, this prefix query is executed as a part of the user's query. This limits the results accordingly to match the user's role.

Note: Please note that function calls like `withCauseOf` and `withNeighborsOf` aren't supported as they would not be performant in this context.

If a user belongs to multiple groups, then this user can have multiple scopes, which translates to multiple prefixes. In this situation, the prefix is executed as an OR of all scopes that this user has.

Users need to log out and authenticate again to SUSE Observability whenever any changes to roles or permissions are made.

## Why scopes?

Scopes are introduced as a security feature that's mandatory for every subject within SUSE Observability. The predefined SUSE Observability users Administrator, Power User and Guest roles have no scope defined.

It's possible to specify a scope as a query wildcard, however, this will result in access to everything and isn't recommended. If there is a need for access without a scope, it's recommended to use one of the [predefined roles](rbac_permissions.md#predefined-roles) instead.

## Examples

The below example shows the same topology view called "All Infrastructure" for four users with different permission levels.

### This user is a part of SUSE Observability Admin group, so there is no scope:

![Full view permissions](../../../.gitbook/assets/v51_allperm.png)

The query for this view is the same as for the others, but without any prefix:

```text
'layer = "Infrastructure" AND domain IN ("Customer1", "Customer2")'
```

### Below user is in a group with configured subject X with the following scope:

```text
'domain = "Customer1"'
```

![Limited view](../../../.gitbook/assets/v51_esx1perm.png)

Query with the prefix for this view is:

```text
'(domain = "Customer1") AND (layer = "Infrastructure" AND domain IN ("Customer1", "Customer2"))'
```

### Another user who is a part of a group with a configured subject Y that has the following scope:

```text
'domain = "Customer2"'
```

gets this topology:

![Limited view](../../../.gitbook/assets/v51_esx2perm.png)

Query with the prefix for this view is:

```text
'(domain = "Customer2") AND (layer = "Infrastructure" AND domain IN ("Customer1", "Customer2"))'
```

### User with multiple prefixes

It's possible to assign a subject to more than just one group. In this example, you can see an Infrastructure Manager who can see the whole view presented above. This user has to be in both groups that have configured subjects as X and Y. In this case, the prefix for the user query will look like the following:

```text
'(domain = "Customer1" OR domain = "Customer2")'
```

Query with prefix for this user is then:

```text
'(domain = "Customer1" OR domain = "Customer2") AND (layer = "Infrastructure" AND domain IN ("Customer1", "Customer2"))'
```

Which results in a following view:

![Full view permissions](../../../.gitbook/assets/v51_allperm.png)

