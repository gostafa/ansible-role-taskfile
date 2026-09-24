# Ansible Role: Taskfile

Install [Task](https://taskfile.dev/) (Taskfile) on Linux from the official GitHub releases.

## Features

- Installs the latest stable Task release by default.
- Supports exact versions such as `3.53.1` or `v3.53.1`.
- Automatically detects `amd64`, `arm64`, `arm`, `386`, and `riscv64`.
- Supports explicit architecture overrides.
- Verifies the release archive with the official SHA-256 checksum file.
- Idempotent: does not download or replace Task when the requested version is already installed.
- Customizable GitHub API, download base URL, installation path, owner/group, mode, TLS verification, timeout, and temp directory.

## Requirements

- Ansible 2.15+
- Linux target
- Permission to write to `taskfile_install_dir` (use `become: true` for `/usr/local/bin`)
- Target has `tar`/gzip support required by Ansible's `unarchive` module.

## Role Variables

### Version

```yaml
taskfile_version: latest
```

Install an exact version:

```yaml
taskfile_version: "3.53.1"
```

or:

```yaml
taskfile_version: "v3.53.1"
```

### Installation

```yaml
taskfile_install_dir: /usr/local/bin
taskfile_binary_name: task
taskfile_owner: root
taskfile_group: root
taskfile_mode: "0755"
taskfile_force: false
```

### Architecture

Automatic detection is used by default. Override it when needed:

```yaml
taskfile_arch: arm64
```

### Custom endpoints / mirrors

```yaml
taskfile_github_api_url: https://api.github.com
taskfile_github_repository: go-task/task
taskfile_download_base_url: https://github.com/go-task/task/releases/download
```

These can be changed for an internal proxy or mirror while keeping the role interface unchanged.

### HTTP / TLS

```yaml
taskfile_validate_certs: true
taskfile_http_timeout: 30
```

### Temporary files

```yaml
taskfile_temp_dir: /tmp/ansible-taskfile
taskfile_cleanup: true
```

## Usage

```yaml
---
- name: Install Task
  hosts: all
  become: true

  roles:
    - role: gostafa.taskfile
```

Install a pinned version:

```yaml
---
- name: Install pinned Task version
  hosts: all
  become: true

  roles:
    - role: gostafa.taskfile
      vars:
        taskfile_version: "3.53.1"
```

Install in a custom directory as a non-root user:

```yaml
---
- name: Install Task locally
  hosts: all

  roles:
    - role: gostafa.taskfile
      vars:
        taskfile_install_dir: "{{ ansible_env.HOME }}/.local/bin"
        taskfile_owner: "{{ ansible_user_id }}"
        taskfile_group: "{{ ansible_user_gid | default(ansible_user_id) }}"
```

## Installation with requirements.yml

```yaml
---
roles:
  - name: gostafa.taskfile
    version: "1.0.0"
```

Then:

```bash
ansible-galaxy role install -r requirements.yml
```

## Local testing

Syntax check:

```bash
ansible-playbook -i tests/inventory tests/test.yml --syntax-check
```

Run the test playbook:

```bash
ansible-playbook -i tests/inventory tests/test.yml
```

## Publishing

5. Import the repository into Ansible Galaxy.
6. Create a SemVer Git tag such as `v1.0.0` for each role release.

## License

MIT
