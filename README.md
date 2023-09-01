# broken-api-operator

I created this repo to create an operator that introduces an API at v0.0.1 and then introduces a breaking API change at v0.0.2 to prove that OLM will not allow the upgrade to proceed if it would invalidate an existing CR. I then prove that OLM will allow the upgrade to proceed once the CR is updated such that it passes the validation introduced in version v0.0.2.

[![asciicast](https://asciinema.org/a/8PJRxSCUD6tJEMeGy8gAMP2TV.svg)](https://asciinema.org/a/8PJRxSCUD6tJEMeGy8gAMP2TV)

## Steps to build a bundle

Start here: https://sdk.operatorframework.io/docs/building-operators/golang/quickstart/#steps


Example commands for v0.0.2:
```bash
$ make docker-build docker-push IMG="docker.io/awgreene/broken-api-operator:v0.0.2"
$ make bundle IMG="docker.io/awgreene/broken-api-operator:v0.0.2"
$ make bundle-build bundle-push BUNDLE_IMG="docker.io/awgreene/broken-api-operator-bundle:v0.0.2"
```
## Steps to build an Index

Follow the steps here: https://olm.operatorframework.io/docs/tasks/creating-a-catalog/#catalog-creation-with-raw-file-based-catalogs


Commands used for this catalog:
```bash
$ opm init broken-api-operator --default-channel=stable --output yaml > cool-catalog/broken-api-operator.yaml
$ opm render docker.io/awgreene/broken-api-operator-bundle:v0.0.1 --output=yaml >> cool-catalog/broken-api-operator.yaml
$ opm render docker.io/awgreene/broken-api-operator-bundle:v0.0.2 --output=yaml >> cool-catalog/broken-api-operator.yaml
$ cat << EOF >> cool-catalog/broken-api-operator.yaml
---
schema: olm.channel
package: broken-api-operator
name: stable
entries:
  - name: broken-api-operator.v0.0.1
  - name: broken-api-operator.v0.0.2
    replaces: broken-api-operator.v0.0.1
EOF
```