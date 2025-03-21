<div style="margin: 20px;">
  <img src="docs/images/nexlayer.png" alt="Nexlayer GitHub Banner">
</div>

# Langfuse

This repository is a fork of [langfuse/langfuse](https://github.com/langfuse/langfuse), enhanced with Nexlayer integration via a `nexlayer.yaml` configuration file.

## Table of Contents

1. [Demo Langfuse](#demo-langfuse)
2. [The Langfuse `nexlayer.yaml` file](#the-langfuse-nexlayeryaml-file)
3. [`nexlayer.yaml` Pod Configuration Schema](#nexlayeryaml-pod-configuration-schema)

## Demo Langfuse

Ready to see Langfuse in action? Follow these simple steps to demo the application:
1. **Fork this repository** to create a personal copy.
2. **Run the following cURL command** in the langfuse directory. This will generate a URL to view your Langfuse application:
```bash
curl -X POST https://app.nexlayer.io/startUserDeployment -H "Content-type: text/x-yaml" --data-binary @nexlayer.yaml
```

**Launch Your Demo**: Once deployed, click on the provided URL to explore your Langfuse application live!

## The Langfuse `nexlayer.yaml` File

### Important Note on Images Used
The `nexlayer.yaml` file includes both the `langfuse/langfuse` and `langfuse/langfuse-worker` images tagged at version `2.95.1`.  Later images produce errors causing the Langfuse application to fail on startup.

### Environment Variables
Shown below are the set environment variables for the `langfuse-web` pod.  Some important notes:
- The `NEXTAUTH_URL` variable is set to the Nexlayer provided `<% URL %>` scriptlet.  Nexlayer will replace this scriptlet with the URL of your deployed site.
- Connecting to other pods: variables such as `DATABASE_URL`, `CLICKHOUSE_URL` and `REDIS_HOST` include connection strings to other pods in the deployment.  Here, each uses the Nexlayer `<pod-name>.pod` notation to set the hostname of the pod to connect to.  For example, `CLICKHOUSE_URL` is set to `http://clickhouse.pod:8123` where `clickhouse` is the name given to the pod we want to connect to.  `clickhouse.pod` in turn provides the hostname at which we can reach the `clickhouse` pod at.

### `nexlayer.yaml`
```yaml
vars:
  DATABASE_URL: postgresql://postgres:postgres@postgres.pod:5432/postgres
  DIRECT_URL: postgresql://postgres:postgres@postgres.pod:5432/postgres
  SALT: mysalt
  ENCRYPTION_KEY: "0000000000000000000000000000000000000000000000000000000000000000"
  TELEMETRY_ENABLED: "true"
  NEXTAUTH_URL: <% URL %> 
  NEXTAUTH_SECRET: mysecret
  CLICKHOUSE_CLUSTER_ENABLED: "false"
  CLICKHOUSE_MIGRATION_URL: clickhouse://clickhouse.pod:9000
  CLICKHOUSE_URL: http://clickhouse.pod:8123
  CLICKHOUSE_USER: clickhouse
  CLICKHOUSE_PASSWORD: clickhouse
  REDIS_HOST: redis.pod
  REDIS_PORT: "6379"
  REDIS_AUTH: myredissecret
```

Want more information?  View the Nexlayer template documentation [here](https://github.com/Nexlayer/templates/blob/main/README.md).

## `nexlayer.yaml` Pod Configuration Schema
| Key | Definition | Why it matters | Examples |
|-----|------------|----------------|----------|
| **name** | A unique name to identify this service. | Each little machine (pod) must work correctly for your app to run—if one machine breaks, your whole app might not work and your friends wouldn't be able to use it. | `name: langfuse-web` |
| **image** | Specifies the Docker container image (including repository info) to deploy for that pod. | This tells Nexlayer exactly which pre-built container to use for your live app. Choosing a solid image means your app runs in a proven, ready-to-go environment for all your users. | `image: "langfuse/langfuse:2.95.1"` |
| **path** | For web-facing pods, defines the external URL route where users access the service. | This sets the web address path where users access your service. A well-defined path means your website, service or API is easily found, making your app look friendly and professional on Nexlayer Cloud. | `path: "/"` or `path: "/api"` |
| **servicePorts** | Defines the ports for external access or inter-service communication. | These ports are like the doorways that let users (or other services) connect to your app. Set them correctly, and your live app will be easily accessible and reliable on the web. | `servicePorts:`<br>`- 3000` |
| **vars** | Runtime environment variables defined as direct key-value pairs. Use `<pod-name>.pod` to reference other pods or `<% URL %>` for the deployment's base URL. | These are the settings that tell your live app how to connect to databases, APIs, and more. When they're set up right, your app adapts perfectly to the cloud environment, keeping your users happy. | `vars:`<br>`  NEXTAUTH_URL: <% URL %>`<br>`  CLICKHOUSE_URL: http://clickhouse.pod:8123`<br>`  REDIS_HOST: redis.pod` |
| **volumes** | Optional persistent storage settings that ensure data isn't lost between restarts. Each volume includes a name, size, and a mountPath. | Volumes are like cloud hard drives for your app. They store important data (like database files) so that nothing is lost when your app updates or restarts, keeping your users' data safe. | `volumes:`<br>`- name: clickhouse-data`<br>`  size: 10Gi`<br>`  mountPath: /var/lib/clickhouse` |
| **mountPath** | Within a volume configuration, specifies the internal file system location where the volume attaches. Must start with a "/". | This tells Nexlayer exactly where to plug in your volume within a running container. When set correctly, your live app can read and save data smoothly—ensuring a seamless user experience. | `mountPath: "/var/lib/clickhouse"` |
