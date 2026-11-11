# ou-login
Log in to OpenUnison-managed Kubernetes clusters with `kubectl openunison-cli`, writing each host to its own kubeconfig file and optionally renaming the generated context, cluster and user to a short name.

## Installation
*Install with homebrew*

```
brew tap peledies/formulae
brew install ou-login
```

This also installs `yq` and `fzf`. You need to install `kubectl` and its `openunison-cli` plugin yourself.

## Configuration
Hosts are listed in `~/.config/openunison-config.yaml`. Set `OU_LOGIN_CONFIG` to use a different path.

If the config file doesn't exist and you're running in a terminal, ou-login offers to create an empty one (`hosts: []`). If you're not in a terminal, it exits with an error.

To add a host without editing the file, run `ou-login add`. It asks for the hostname, an optional context name and the kubeconfig path (default `~/.kube/config-<context>`, or `~/.kube/config` without a context), then appends the entry, creating the config file if needed. It refuses a hostname that's already listed.

```yaml
hosts:
  - hostname: k8sou.playground.example.com
    kubeconfig: ~/.kube/config-playground
    context: playground   # optional
```

| Key | Required | Description |
|-----|----------|-------------|
| `hostname` | yes | OpenUnison host passed to `kubectl openunison-cli login` |
| `kubeconfig` | yes | Kubeconfig file the login writes to (`~` is expanded) |
| `context` | no | Renames the context, cluster and user the login creates to this name, and sets it as `current-context` |

## Usage
```
ou-login                                # pick a host with fzf
ou-login k8sou.playground.example.com   # log in to a specific host
ou-login add                            # add a host to the config
ou-login --version
ou-login --help
```

Before each login, ou-login checks GitHub for a newer release and, if there is one, prints a note suggesting `brew upgrade ou-login`. The check times out after 5 seconds and is skipped if `curl` isn't installed or GitHub can't be reached.

When `context` is set, a re-login replaces the earlier entries of that name with the new ones. If the kubeconfig holds more than one other context, ou-login doesn't rename anything, because it can't tell which one the login created.
