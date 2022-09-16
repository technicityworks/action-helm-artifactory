# Action Helm Artifactory

GitHub Action for packaging, testing helm charts and publishing to Artifactory helm repo

> Note: this action is written to specifically work with Helm repos in Artifactory\_

## Inputs

### Required

`ACTION` - `[check, dependency, lint, package, check_push, push]`

- `check` - Runs all checks on helm chart (dependency build, lint, package)
- `dependency` - Run dependency build on the target helm chart
- `lint` - Run helm lint on the target chart
- `package` - Run helm package on the target chart
- `check_push` - Runs all tests and upload the chart to artifactory
- `push` - Uses helm artifactory plugin to uploads the chart

## Required Environment variables

```yaml
ACTION: # available option documented above
CHART_DIR: path/to/chart # path where the helm chart is located
ARTIFACTORY_URL: # Artifactory registry https://<company>.jfrog.io/<company>
ARTIFACTORY_USERNAME: ${{ secrets.ARTIFACTORY_USERNAME }} # ARTIFACTORY_USERNAME (Artifactory username) must be set in GitHub Repo secrets
ARTIFACTORY_PASSWORD: ${{ secrets.ARTIFACTORY_PASSWORD }} # ARTIFACTORY_PASSWORD (Artifactory api key) must be set in GitHub Repo secrets
```

## Optional Environment variables

```yaml
IGNORE: # ignore versio version lookup (used to avoid overwriting existing chart version)
CHART_VERSION: # Override helm chart version when pushing
HELM_VERSION: # Override helm version. Default "3.5.1"
HELM_ARTIFACTORY_PLUGIN_VERSION: # Override helm artifactory plugin version. Default "v1.0.2"
CHART_VERSION: # if defined override version in Chart.yaml. Default is unset
```

## Example workflow

Perform all checks on pull requests

```yaml
name: Helm lint, test, package and publish

on: pull_request

jobs:
  helm-suite:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      # - name: myOtherJob1
      #   run:

      - name: "Helm checks"
        uses: technicityworks/action-helm-artifactory@v1.0.0
        env:
          ACTION: "check"
          CHART_DIR: chart
          ARTIFACTORY_URL: https://artifactory.example.com/helm/
          ARTIFACTORY_USERNAME: ${{ secrets.ARTIFACTORY_USERNAME }}
          ARTIFACTORY_PASSWORD: ${{ secrets.ARTIFACTORY_PASSWORD }}
```

Push helm charts on merge/commits on main branch

```yaml
name: Helm lint, test, package and publish

on:
  push:
    branches: ["main"]

jobs:
  helm-suite:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      # - name: myOtherJob1
      #   run:

      - name: "Helm publish"
        uses: technicityworks/action-helm-artifactory@v1.0.0
        env:
          ACTION: "push"
          CHART_DIR: chart
          ARTIFACTORY_URL: https://artifactory.example.com/helm/
          ARTIFACTORY_USERNAME: ${{ secrets.ARTIFACTORY_USERNAME }}
          ARTIFACTORY_PASSWORD: ${{ secrets.ARTIFACTORY_PASSWORD }}
```

> Adapted from [mbovo/action-helm-artifactory](https://github.com/mbovo/action-helm-artifactory)
