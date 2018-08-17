# Java Language Server

This folder contains the deployment files for the Java Language server.

## How to Deploy

You can enable it by:

1. Running `kubectl apply -f configure/xlang/java/ --recursive` to apply the deployment files to your cluster

```shell
> kubectl apply -f configure/xlang/java/ --recursive

deployment "xlang-java-bg" created
service "xlang-java-bg" created
deployment "xlang-java" created
service "xlang-java" created
```

2. Adding the following environment variables to the `lsp-proxy` deployment to make it aware of the
   Java language server's existence:

`base/lsp-proxy/lsp-proxy.Deployment.yaml`:

```yaml
env:
  - name: LANGSERVER_JAVA
    value: tcp://xlang-java:2088
  - name: LANGSERVER_JAVA_BG
    value: tcp://xlang-java-bg:2088
```

## Gradle and Aritfactory Configuration

The `xlang-java` and `xlang-java-bg` deployments support configuring Gradle and Artifactory via the following environment variables:

```yaml
env:
  # A comma-delimited list of patterns that selects repository revisions
  # for which to execute Gradle scripts,rather than extracting Gradle metadata statically.
  #
  # **Security note**: these should be restricted to repositories within your own organization.
  #
  # A percent sign (`%`) can be used to prefix-match. For example, `git://my.internal.host/org1/%
  # git://my.internal.host/org2/repoA?%` would select all revisions of all repositories in `org1`
  # and all revisions of `repoA` in `org2`.
  #
  # Note: this field is misnamed, as it matches against the `originalRootURI` LSP initialize parameter,
  # rather than the no-longer-used `originalRootPath` parameter.
  - name: EXECUTE_GRADLE_ORIGINAL_ROOT_PATHS
    value: "git://my.internal.host/org1/%,git://my.internal.host/org2/repoA?%"

  # Private artifact repository ID in your build files. If you do not explicitly include the private artifact
  # repository, then set this to some unique string (e.g,. `my-repository`).
  - name: PRIVATE_ARTIFACT_REPO_ID
    value: my-repository

  # The URL that corresponds to `PRIVATE_ARTIFACT_REPO_ID`
  # (e.g., `http://my.artifactory.local/artifactory/root`).
  - name: PRIVATE_ARTIFACT_REPO_URL
    value: "http://my.artifactory.local/artifactory/root"

  # The username to authenticate to the private Artifactory.
  - name: PRIVATE_ARTIFACT_REPO_USERNAME
    value: my_username

  # The password to authenticate to the private Artifactory.
  - name: PRIVATE_ARTIFACT_REPO_PASSWORD
    value: hunter2
```