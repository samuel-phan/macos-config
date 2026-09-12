# macos-config

This configuration is for macOS.

## Prerequisites

### uv

Install [`uv`](https://docs.astral.sh/uv/):

```shell
brew install uv
```

### pre-commit

Install `pre-commit`:

```shell
brew install pre-commit
```

Install `pre-commit` hooks:

```shell
pre-commit install
```

### Ansible

Create the Python virtualenv `.venv` for this project:

```shell
uv sync
```

You have 2 choices to active the Python virtualenv:

1. Manually:

    ```shell
    source .venv/bin/activate
    ```

2. Automatically using [direnv](https://direnv.net/):

    The playbook will do the configuration of direnv, but if you want to do it manually:

    ```shell
    brew install direnv

    mkdir -p ~/.config/direnv
    ln -s "${PWD}/roles/dotfiles/files/direnv/direnvrc" ~/.config/direnv
    ```

    Check that in your `~/.zshrc`, the Oh My Zsh plugin `direnv` is enabled:

    ```
    plugins=(direnv git)
    ```

    - Open a new shell.
    - Check the content of [`.envrc`](.envrc):

        ```shell
        cat .envrc
        ```

    - If everything is fine, then you can allow `direnv` to automatically load the Python virtualenv when cd'ing into this repository:

        ```shell
        direnv allow
        ```

    If the [`.envrc`](.envrc) file changes, you need to allow it again. This is for security purpose.

### Sudo password for some Homebrew casks that need sudo access

Some Homebrew casks need sudo access, but Homebrew shouldn't be invoked as `root`, so the only solution is to set the `SUDO_ASKPASS`, and not use the regular Ansible become mechanism.

Create a `vault-password` file:

```shell
LC_ALL=C tr -dc 'A-Za-z0-9!@#$%^&*' < /dev/urandom | head -c 32 > vault-password
```

Create a file `inventory/host_vars/localhost/vault_become_password.yml`:

```shell
echo "vault_ansible_become_password: CHANGEME" > inventory/host_vars/localhost/vault_become_password.yml
```

Encrypt the file `inventory/host_vars/localhost/vault_become_password.yml` with Ansible Vault:

```shell
ansible-vault encrypt inventory/host_vars/localhost/vault_become_password.yml
```

Edit the file `inventory/host_vars/localhost/vault_become_password.yml` and set your sudo password:

```shell
ansible-vault edit inventory/host_vars/localhost/vault_become_password.yml
```

## Run the provisioning

```shell
ansible-playbook init.yml
```

## Things to configure manually

### Visual Studio Code

Go to <https://code.visualstudio.com/>.

### Chrome Apps

In Chrome:

- Click on the Apps button in the Bookmarks bar
- Right-click on the app to install > Install on this device

This way, you can keep the Chrome App ID (used for application shortcuts).

## Developer notes

### Python packages management

To list the available version of a package, e.g. `ansible` package:

```shell
uvx pip index versions ansible
```
