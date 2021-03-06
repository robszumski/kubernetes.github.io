---
title: Kubernetes Object Management
---

{% capture overview %}
The `kubectl` command-line tool supports several different ways to create and manage
Kubernetes objects. This document provides an overview of the different
approaches.
{% endcapture %}

{% capture body %}

## Management techniques

**Warning:** A Kubernetes object should be managed using only one technique. Mixing
and matching techniques for the same object results in undefined behavior.

| Management technique             | Operates on          |Recommended environment | Supported writers  | Learning curve |
|----------------------------------|----------------------|------------------------|--------------------|----------------|
| Imperative commands              | Live objects         | Development projects   | 1+                 | Lowest         |
| Imperative object configuration  | Individual files     | Production projects    | 1                  | Moderate       |
| Declarative object configuration | Directories of files | Production projects    | 1+                 | Highest        |

## Imperative commands

When using imperative commands, a user operates directly on live objects
in a cluster. The user provides operations to
the `kubectl` command as arguments or flags.

This is the simplest way to get started or to run a one-off task in
a cluster. Because this technique operates directly on live
objects, it provides no history of previous configurations.

### Examples

Run an instance of the nginx container by creating a Deployment object:

```sh
kubectl run nginx --image nginx
```

Do the same thing using a different syntax:

```sh
kubectl create deployment nginx --image nginx
```

### Trade-offs

Advantages compared to object configuration:

- Commands are simple, easy to learn and easy to remember.
- Commands require only a single step to make changes to the cluster.

Disadvantages compared to object configuration:

- Commands do not integrate with change review processes.
- Commands do not provide an audit trail associated with changes.
- Commands do not provide a source of records except for what is live.
- Commands do not provide a template for creating new objects.

## Imperative object configuration

When using imperative object configuration, a user operates on object
configuration files stored locally. An object configuration file defines a full
object in either YAML or JSON. 

The user provides an operation (create, replace, delete), one or more files,
and flags to the `kubectl` command.

This technique requires a deep understanding of the Kubernetes
object definitions.

**Note:** While this technique defines the object itself through a declarative
configuration file, the operations are imperative: create, replace, delete.

### Examples

Create the objects defined in a configuration file:

```sh
kubectl create -f nginx.yaml
```

Delete the objects defined in two configuration files:

```sh
kubectl delete -f nginx.yaml -f redis.yaml
```

Update the objects defined in a configuration file by overwriting
the live configuration:

```sh
kubectl replace -f nginx.yaml
```

### Trade-offs

Advantages compared to imperative commands:

- Object configuration can be stored in a source control system such as Git.
- Object configuration can integrate with processes such as reviewing changes before push and audit trails.
- Object configuration provides a template for creating new objects.

Disadvantages compared to imperative commands:

- Object configuration requires basic understanding of the object schema.
- Object configuration requires the additional step of writing a YAML file.

Advantages compared to declarative object configuration:

- Imperative object configuration behavior is simpler and easier to understand.
- As of Kubernetes version 1.5, imperative object configuration is more mature.

Disadvantages compared to declarative object configuration:

- Imperative object configuration works best on files, not directories.
- Updates to live objects must be reflected in configuration files, or they will be lost during the next replacement.

## Declarative object configuration

When using declarative object configuration, a user operates on object
configuration files stored locally, however the user does not define the
operations to be taken on the files. Create, update, and delete operations
are automatically detected per-object by `kubectl`. This enables working on
directories, where different operations might be needed for different objects.

**Note:** Declarative object configuration retains changes made by other
writers, even if the changes are not merged back to the object configuration file.
This is possible by using the `patch` API operation to write only
observed differences, instead of using the `replace`
API operation to replace the entire object configuration.

### Examples

Process all object configuration files in the `configs` directory, and
create or patch the live objects:

```sh
kubectl apply -f configs/
```

Recursively process directories:

```sh
kubectl apply -R -f configs/
```

### Trade-offs

Advantages compared to imperative object configuration:

- Changes made directly to live objects are retained, even if they are not merged back into the configuration files.
- Declarative object configuration has better support for operating on directories and automatically detecting operation types (create, patch, delete) per-object.

Disadvantages compared to imperative object configuration:

- Declarative object configuration is harder to debug and understand results when they are unexpected.
- Partial updates using diffs create complex merge and patch operations.

{% endcapture %}

{% capture whatsnext %}
- [Managing Kubernetes Objects Using Imperative Commands](/docs/concepts/tools/kubectl/object-management-using-imperative-commands/)
- [Managing Kubernetes Objects Using Object Configuration (Imperative)](/docs/concepts/tools/kubectl/object-management-using-imperative-config/)
- [Managing Kubernetes Objects Using Object Configuration (Declarative)](/docs/concepts/tools/kubectl/object-management-using-declarative-config/)
- [Kubectl Command Reference](/docs/user-guide/kubectl/v1.5/)
- [Kubernetes Object Schema Reference](/docs/resources-reference/v1.5/)

{% comment %}
{% endcomment %}
{% endcapture %}

{% include templates/concept.md %}
