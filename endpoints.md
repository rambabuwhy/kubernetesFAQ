# Endpoints

Suppose you have a Kubernetes cluster running a web application with multiple instances (pods) of a service called "web-server". You want to expose this service to the outside world so that users can access it.

1.  **Create Pods**: First, you deploy several pods running your web server application. Each pod represents an instance of your web server.

    ```yaml
    yamlCopy codeapiVersion: v1
    kind: Pod
    metadata:
      name: web-server-1
    spec:
      containers:
      - name: web-server
        image: your-web-server-image
        ports:
        - containerPort: 80
    ```

    You would replicate this Pod definition to create multiple instances of your web server, such as `web-server-2`, `web-server-3`, and so on.
2.  **Create a Service**: Next, you create a Service resource to abstract and load balance the traffic across all instances of your web server pods.

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: web-service
    spec:
      selector:
        app: web-server
      ports:
      - protocol: TCP
        port: 80
        targetPort: 80
    ```

    Here, the `selector` field specifies that the Service should route traffic to pods labeled with `app: web-server`. The Service exposes port 80, which is the default HTTP port.
3.  **Endpoints**: When you create the `web-service` Service, Kubernetes automatically creates an associated Endpoints object.

    ```yaml
    apiVersion: v1
    kind: Endpoints
    metadata:
      name: web-service
    subsets:
    - addresses:
      - ip: <pod1-IP-address>
      - ip: <pod2-IP-address>
      - ip: <pod3-IP-address>
      ports:
      - port: 80
    ```

    This Endpoints object dynamically updates to include the IP addresses of all pods labeled with `app: web-server`. If you scale up or down the number of pods, the Endpoints object will reflect those changes.
4. **Accessing the Service**: Clients can now access your web service using the ClusterIP of the `web-service`. When they do so, Kubernetes routes the traffic to one of the pods listed in the Endpoints associated with `web-service`.

In summary, Endpoints dynamically maintain the list of IP addresses and ports of the pods that a Service routes traffic to. This abstraction allows you to scale your application without needing to manually manage IP addresses or routing configurations.
