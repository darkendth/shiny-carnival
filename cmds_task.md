# Commands

## Ansible

- [https://www.digitalocean.com/community/cheatsheets/how-to-use-ansible-cheat-sheet-guide](Ansible cheatseat)

```bash
$ ansible-playbook -i inventory.ini  --syntax-check logrotate.yml
to check syntax of yaml file.

$ ansible all -i inventory  -m ping -e ansible_ssh_connection=ssh -e ansible_ssh_user= --ask-pass  -v

$ ansible-playbook -i inventory.ini  --check logrotate.yml --user= 

$ ansible-playbook -i inventory  logrotate.yml -e --user= --ask-pass --ask-become-pass

$ ansible-playbook -i inventory logrotate.yml --extra-vars "user=username group=groupname"
to pass parameters.
```

## Basic commands

```bash
$ passwd
change password

$ adduser demo

$ find . -type f -size +1G -exec ls -lh {} +
go to every directory form curent folder and list all files whose size is greater than 1 GB.

$ find . -maxdepth 2 -type f -name "*-json.log*" -exec ls -lh {} + | awk '{print $5, $9}'

```

## Git

```bash (git)
$ ssh -T git@host-name
to check whether the key have access to server git.

$ vi ~/.ssh/config
Host host-name
    HostName host-name
    User git
    IdentityFile ~/.ssh/id_ghe

$ git push origin prod
send branch to git

$ git push --set-upstream origin dev

$ git config --global credential.helper store
avoid typing username and password every time on push and pull. only first time is required. credentials are stored in a plaintext format within a .git-credentials file.

$ git config --global credential.helper
to check the configured credentials.

```

if you have changes to your last commit, you don't have to create another commit altogether, you can use --amend flag.

```bash (git)
$ git add .
add 

$ git commit --amend
This will open your default text editor to modify the commit message if needed.

$ git push origin your_branch --force
```

### Commit Message Formats

```bash
<type>(<optional scope>): <description>
empty separator line
<optional body>
empty separator line
<optional footer>
```

```bash
$ Merge branch '<branch name>

$ Chore: init
Initial Commit 
```

Types:

- Api relevant changes
  - `feat` commits, that add or remove a new feature.
  - `fix` commits, that fixes a bug
- `refactor` commits, that rewrite/restructure your code, not change API behaviour.
  - `perf` commits, that improve performance.
- `style` commits, adjusting white-spaces, formatting, missing semi-colons, etc.
- `test` commits, that add missing tests or correcting existing tests.
- `docs` commits, that affect documentaton only
- `build` commits, that affect build component like build tool, ci pipeline, dependencies, project version.
- `ops` commits, that affect operational component like infrastructure, deployment, backup, recovery..
- `chore` miscellaneous commits, modifying .gitignore.

Scope: optional

Description:

- use imperative, present tense.
- Don't capitalize the first letter.
- No dot at the end.

Body: place to mention issues identifiers and their relations.

Footer: contain any information about Breaking changes and is also the place to reference issues that this commit refers to,

- `Breaking changes:` followed by spaces or two new lines.

#### Examples

```bash
$ feat!: remove ticket list endpoint

refers to JIRA-1337

BREAKING CHANGES: ticket endpoints no longer support list all entities.

$ fix(api): fix wrong calculation of request body checksum

$ build(release): bump version to 1.0.0
```

#### Setup with the pre-commit framework

Create `.pre-commit-config.yaml` file in the root directory of your repository with following content.

```yaml
repos:
- repo: https://github.com/qoomon/git-conventional-commits
  rev: <RELEASE_TAG>
  hooks:
    - id: conventional-commits
```

- Install the pre-commit framework `pip install pre-commit`
- Install the commit-msg hook `pre-commit install -t commit-msg`.

## Kubernetes

```bash (kubectl)
$ kubectl get nodes | awk '{print $1}'
list nodes name (col 1)

$ kubectl get nodes | awk 'NR > 1 {print $1}'
skip first row also.


```

```bash (dgraph)
$ curl -X POST localhost:8080/admin/schema -d '@schema.graphql'
upload schema to dgrpah

```
