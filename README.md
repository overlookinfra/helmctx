# helmctx

`helmctx` is a simple wrapper around [Helm](https://helm.sh/) that helps manage
configurations for different clusters. Specifically, because Helm stores TLS
configuration in `HELM_HOME`, and because that configuration often varies by
cluster, it is inconvenient for the same home directory to be used across
clusters.

This script simply sets your `HELM_HOME` to the result of evaluating
`"~/.local/share/helmctx/$( kubectl config current-context )"` in your shell and
then invokes Helm normally.

## Customization

The `helmctx` command understands the following environment variables:

* `HELMCTX_KUBECTL`: The path to the `kubectl` command. If not specified,
  attempts to find in `$PATH`.
* `HELMCTX_HELM`: The path to the real `helm` command (see [below](#using-helmctx-as-helm)). If not specified, attempts to find in `$PATH`.
* `HELMCTX_BASEDIR`: The parent directory to use for Helm home directories.
  Defaults to `~/.local/share/helmctx`.

## Installation

We recommend cloning `helmctx` locally and using a `bin` directory in your
user's `$HOME`. Add `~/bin` to your `$PATH` in `.profile`, `.bashrc`, `.zshrc`,
etc. if not already present:

```shell
export PATH="$HOME/bin:$PATH"
```

Then clone the repository and link the wrapper:

```
~/src$ git clone git://github.com/puppetlabs/helmctx.git
~/src$ ln -snf ~/src/helmctx/bin/helmctx ~/bin/helmctx
```

If the wrapper script changes, simply `git pull` to update your local copy! We
will ensure that the current `master` branch of this repository is always in a
stable, production-ready state.

## Usage

Use `helmctx` like `helm`:

```
~$ helmctx home
/home/me/.local/share/helmctx/gke_example_us-west1_test
~$ kubectl config use-context gke_other_us-west1_prod
Switched to context "gke_other_us-west1_prod".
~$ helmctx home
/home/me/.local/share/helmctx/gke_other_us-west1_prod
```

### Using `helmctx` as `helm`

It is possible to link `helmctx` as the Helm binary as long as you keep the real
`helm` in your `$PATH` or set `$HELMCTX_HELM` to the proper path to the real
binary.

```
~/src$ ln -snf ~/src/helmctx/bin/helmctx ~/bin/helm
~/src$ HELMCTX_HELM=~/my/real/helm helm home
/home/me/.local/share/helmctx/gke_example_us-west1_test
```
