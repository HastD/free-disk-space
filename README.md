# Free disk space on Ubuntu GitHub Actions runner

This action quickly frees disk space on Ubuntu runners.

The Linux (Ubuntu) GitHub actions runners have 72 GB of total disk space, but
only 19 GB is initially available due to the large number of tools preinstalled
on the runners. We can free up a lot of disk space by removing tools that most
workflows don't need. After running this action with default options, there
should be about 63 GB of free space.

Inspired by [jlumbroso/free-disk-space] and [ublue-os/remove-unwanted-software].

## Example

```yaml
jobs:
  example:
    runs-on: ubuntu-24.04
    steps:
      - name: Free disk space
        uses: hastd/free-disk-space@v0.1.0
```

## Goals

- Free lots of disk space by deleting tools that most workflows don't use.
- Run in under 90 seconds.
- Defaults should work for most workflows.
- Allow customization to exclude or include paths.

## Usage

By default, this action deletes the swapfile and the following directories:

| Path                          | Description                          |
| ----------------------------- | ------------------------------------ |
| `/etc/skel`                   | Home directory template              |
| `/home/packer`                | Home directory for `packer` user     |
| `/opt/az`                     | Microsoft Azure CLI                  |
| `/opt/google`                 | Google Chrome                        |
| `/opt/microsoft`              | Microsoft Edge, PowerShell           |
| `/opt/pipx`                   | pipx (Python package installer)      |
| `/opt/hostedtoolcache/CodeQL` | CodeQL                               |
| `/opt/hostedtoolcache/PyPy`   | PyPy                                 |
| `/opt/hostedtoolcache/Python` | Python (various versions & packages) |
| `/opt/hostedtoolcache/Ruby`   | Ruby                                 |
| `/opt/hostedtoolcache/go`     | golang                               |
| `/opt/hostedtoolcache/node`   | Node.js                              |
| `/usr/lib/firefox`            | Firefox                              |
| `/usr/lib/google-cloud-sdk`   | Google Cloud SDK                     |
| `/usr/lib/jvm`                | Java                                 |
| `/usr/lib/llvm-*`             | LLVM (versions 16, 17, 18)           |
| `/usr/local/.ghcup`           | Haskell                              |
| `/usr/local/aws-cli`          | AWS CLI                              |
| `/usr/local/aws-sam-cli`      | AWS SAM CLI                          |
| `/usr/local/julia1.12.1`      | Julia                                |
| `/usr/local/lib/android`      | Android SDK                          |
| `/usr/local/lib/node_modules` | Node.js modules                      |
| `/usr/local/share/chromium`   | Chromium                             |
| `/usr/local/share/powershell` | PowerShell                           |
| `/usr/share/az_12.5.0`        | Microsoft Azure CLI                  |
| `/usr/share/miniconda`        | Miniconda                            |
| `/usr/share/dotnet`           | .NET                                 |
| `/usr/share/swift`            | Swift                                |

To exclude one of the above directories from removal, use the `exclude` option.

To include additional paths, use the `include` option.

To skip disabling swap and removing the swapfile, pass `swapoff: false` to the
action.

For example:

```yaml
jobs:
  example:
    runs-on: ubuntu-24.04
    steps:
      - name: Free disk space
        uses: hastd/free-disk-space@v0.1.0
        with:
          exclude: |
            /opt/hostedtoolcache/Python
            /usr/local/lib/android
          include: |
            /some/other/path
          swapoff: false
```

## FAQ

### How was the list of directories determined?

I ran `du -hs` on a bunch of directories in a GitHub actions runner to find what
was taking up a lot of space.

If you see something else that should be added, please create an issue or a PR.

### How are files removed?

Files are removed using `rm -rf`. We don't use `apt` to remove packages because
that's slower.

This does break bunch of packages whose files are removed. If you try to install
a package that depends on one of these packages after running this action, it
will probably fail.

[jlumbroso/free-disk-space]: https://github.com/jlumbroso/free-disk-space
[ublue-os/remove-unwanted-software]:
  https://github.com/ublue-os/remove-unwanted-software
