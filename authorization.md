# Authorization

In Kubernetes, authorization is the process of determining whether a user or process has the necessary permissions to perform a requested action. Kubernetes uses Role-Based Access Control (RBAC) to manage authorization.

Here's an overview of how authorization works in Kubernetes:

1. **Authentication**: Before authorization can occur, Kubernetes must first authenticate the user or process making the request. Authentication verifies the identity of the entity attempting to access the cluster. Kubernetes supports various authentication methods, including client certificates, bearer tokens, and integrated authentication providers like LDAP or OAuth.
2. **RBAC**: Role-Based Access Control (RBAC) is the primary authorization mechanism in Kubernetes. RBAC allows cluster administrators to define roles and role bindings, which specify what actions users or processes are allowed to perform within the cluster.
   * **Roles**: Roles define a set of permissions for a specific namespace.
   * **ClusterRoles**: ClusterRoles define permissions across the entire cluster.
   * **RoleBindings**: RoleBindings bind roles to specific users, groups, or service accounts within a namespace.
   * **ClusterRoleBindings**: ClusterRoleBindings bind cluster roles to users, groups, or service accounts across the entire cluster.
3. **Attribute-Based Access Control (ABAC)**: While RBAC is the preferred authorization mechanism, Kubernetes also supports Attribute-Based Access Control (ABAC). ABAC allows administrators to define policies based on attributes associated with users, such as usernames or group memberships. However, ABAC is less flexible and scalable compared to RBAC and is considered deprecated.
4. **Webhook Authorization**: Kubernetes also supports webhook authorization, allowing cluster administrators to integrate custom authorization logic using external services. This enables more complex authorization scenarios beyond what RBAC and ABAC offer.
5. **Default Policies**: Kubernetes comes with default policies that provide some basic security out of the box. For example, by default, only the cluster administrator has full access to the cluster, and other users have restricted permissions until explicitly granted additional access through RBAC rules.
6. **Auditing**: Kubernetes can log authorization decisions for auditing purposes, allowing administrators to track who accessed the cluster and what actions they performed.

Overall, Kubernetes provides a flexible and powerful authorization framework through RBAC, allowing cluster administrators to control access to cluster resources based on roles and permissions.

Suppose we have a Kubernetes cluster with a namespace called "myapp" and we want to create a role that allows a user to list and view pods within that namespace.

Here's how you can set it up:

1. Create a Role:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: myapp
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

In this YAML file:

* `apiVersion`: Specifies the API version for RBAC resources.
* `kind`: Specifies the type of RBAC resource, which is a "Role" in this case.
* `metadata`: Contains metadata for the Role, including the namespace and name.
* `rules`: Defines the permissions for this Role.
  * `apiGroups`: Specifies the API group for the resources. An empty string ("") indicates the core API group.
  * `resources`: Specifies the Kubernetes resources the Role has permissions for, in this case, "pods".
  * `verbs`: Specifies the actions allowed on the specified resources, in this case, "get" and "list".

2. Apply the Role to the cluster:

```bash
kubectl apply -f role.yaml
```

This creates the "pod-reader" Role in the "myapp" namespace.

3. Bind the Role to a User:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: myapp
subjects:
- kind: User
  name: alice
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

In this YAML file:

* `apiVersion`: Specifies the API version for RBAC resources.
* `kind`: Specifies the type of RBAC resource, which is a "RoleBinding" in this case.
* `metadata`: Contains metadata for the RoleBinding, including the name and namespace.
* `subjects`: Specifies the user or users to bind the Role to. In this example, it binds to a user named "alice".
* `roleRef`: Specifies the Role to bind to the user. It references the "pod-reader" Role created earlier.

4. Apply the RoleBinding to the cluster:

```bash
kubectl apply -f rolebinding.yaml
```

This binds the "pod-reader" Role to the user "alice" within the "myapp" namespace.

Now, user "alice" has permission to list and view pods within the "myapp" namespace, but doesn't have permissions for other resources or namespaces.
