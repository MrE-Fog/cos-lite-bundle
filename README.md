# COS Lite bundle

The lite flavor of Canonical Observability Stack, also called COS Lite, is a light-weight, highly-integrated, Juju-based observability suite running on Kubernetes.

This Juju bundle deploys the stack, consisting of the following interrelated charms:

- [Prometheus](https://charmhub.io/prometheus-k8s) ([source](https://github.com/canonical/prometheus-k8s-operator))
- [Loki](https://charmhub.io/loki-k8s) ([source](https://github.com/canonical/loki-k8s-operator))
- [Alertmanager](https://charmhub.io/alertmanager-k8s) ([source](https://github.com/canonical/alertmanager-k8s-operator))
- [Grafana](https://charmhub.io/grafana-k8s) ([source](https://github.com/canonical/grafana-k8s-operator))
- [Traefik](https://charmhub.io/traefik-k8s) ([source](https://github.com/canonical/traefik-k8s-operator))
- [Catalogue](https://charmhub.io/catalogue-k8s) ([source](https://github.com/canonical/catalogue-k8s-operator))

This bundle is under development.
Join us on [Discourse](https://discourse.charmhub.io/t/canonical-observability-stack/5132) and [MatterMost](https://chat.charmhub.io/charmhub/channels/observability)!

## The Vision

The Canonical Observability Stack is the go-to solution for monitoring Canonical appliances when the end user does not already have an established observability stack. COS Lite, being a flavor of the Canonical Observability Stack, is designed for:

* Best-in-class monitoring of software [charmed with Juju](https://juju.is)
* Limited resource consumption
* High integration and out-of-the-box value
* Running on [MicroK8s](https://microk8s.io/)

With COS Lite now being generally available, we are now working on a highly-available, highly-scalable flavor. It will use many of the same components as COS Lite, plus some additional new ones, and provide the same overall user-experience, and focus on scalability, resilience and broad compatibility with Kubernetes distributions out there.

## Usage

Before deploying the bundle you will most likely want to create a dedicated model for it:

```shell
$ juju add-model cos
$ juju switch cos
```

You can deploy the bundle from charmhub with:

```shell
$ juju deploy cos-lite --trust
```

or, to deploy the bundle from a local file:

```shell
# generate and activate a virtual environment with dependencies
$ tox -e integration --notest
$ source .tox/integration/bin/activate

# render bundle with default values
$ ./render_bundle.py bundle.yaml
$ juju deploy ./bundle.yaml --trust
```

Note: for traefik ingress to work, you may first need to enable the `metallb`
microk8s addon. See the
[tutorial](https://charmhub.io/topics/canonical-observability-stack/tutorials/install-microk8s)
for full details.

The `--trust` option is needed by the charms in the `cos-lite` bundle to be
able to patch their K8s services to:
- use the right ports (see this [Juju limitation](https://bugs.launchpad.net/juju/+bug/1936260))
- apply resource limits

We also make available some [**overlays**](https://juju.is/docs/sdk/bundle-reference) for convenience:

* the [`offers` overlay](https://raw.githubusercontent.com/canonical/cos-lite-bundle/main/overlays/offers-overlay.yaml) exposes as offers the relation endpoints of the COS Lite charms that are likely to be consumed over [cross-model relations](https://juju.is/docs/olm/cross-model-relations).
* the [`storage-small` overlays](https://raw.githubusercontent.com/canonical/cos-lite-bundle/main/overlays/storage-small-overlay.yaml) provides a setup of the various storages for the COS Lite charms for a small setup.
  Using an overlay for storage is fundamental for a productive setup, as you cannot change the amount of storage assigned to the various charms after the deployment of COS Lite.

In order to use the overlays above, you need to:

1. Download the overlays (or clone the repository)
2. Pass the `--overlay <path-to-overlay-file-1> --overlay <path-to-overlay-file-2> ...` arguments to the `juju deploy` command

For example, to deploy the COS Lite bundle with the offers overlay, you would do the following:

```sh
$ curl -L https://raw.githubusercontent.com/canonical/cos-lite-bundle/main/overlays/offers-overlay.yaml -O
$ juju deploy cos-lite --trust --overlay ./offers-overlay.yaml
```

To use COS Lite with machine charms, see
[cos-proxy](https://charmhub.io/cos-proxy)
([source](https://github.com/canonical/cos-proxy-operator)).

## Publishing
```shell
$ tox -e render-edge  # creates bundle.yaml
$ charmcraft pack
$ charmcraft upload cos-lite.zip
$ charmcraft release cos-lite --channel=edge --revision=4
```
