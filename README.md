# Free disk space on Ubuntu GitHub Actions runner

This action quickly frees disk space on Ubuntu runners.

The Linux (Ubuntu) GitHub actions runners have 72 GB of total disk space, but
only 19 GB is initially available due to the large number of tools preinstalled
on the runners. We can free up a lot of disk space by removing tools that most
workflows don't need. After running this action with default options, there
should be about 63 GB of free space.

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

By default, this action disables swap, deletes the swapfile (4 GB), and removes
the following directories:

| Path                          | Description                          | Size    |
| ----------------------------- | ------------------------------------ | ------- |
| `/etc/skel`                   | Home directory template              | 0.68 GB |
| `/home/packer`                | Home directory for `packer` user     | 0.68 GB |
| `/opt/az`                     | Microsoft Azure CLI                  | 0.66 GB |
| `/opt/google`                 | Google Chrome                        | 0.37 GB |
| `/opt/microsoft`              | Microsoft Edge, PowerShell           | 0.78 GB |
| `/opt/pipx`                   | pipx (Python package installer)      | 0.52 GB |
| `/opt/hostedtoolcache/CodeQL` | CodeQL                               | 1.6 GB  |
| `/opt/hostedtoolcache/PyPy`   | PyPy                                 | 0.52 GB |
| `/opt/hostedtoolcache/Python` | Python (various versions & packages) | 1.9 GB  |
| `/opt/hostedtoolcache/Ruby`   | Ruby                                 | 0.22 GB |
| `/opt/hostedtoolcache/go`     | golang                               | 1.1 GB  |
| `/opt/hostedtoolcache/node`   | Node.js                              | 0.57 GB |
| `/usr/lib/firefox`            | Firefox                              | 0.28 GB |
| `/usr/lib/google-cloud-sdk`   | Google Cloud SDK                     | 1.0 GB  |
| `/usr/lib/jvm`                | Java                                 | 1.5 GB  |
| `/usr/lib/llvm-*`             | LLVM (versions 16, 17, 18)           | 1.9 GB  |
| `/usr/local/.ghcup`           | Haskell                              | 6.4 GB  |
| `/usr/local/aws-cli`          | AWS CLI                              | 0.24 GB |
| `/usr/local/aws-sam-cli`      | AWS SAM CLI                          | 0.19 GB |
| `/usr/local/julia1.12.1`      | Julia                                | 1.0 GB  |
| `/usr/local/lib/android`      | Android SDK                          | 12 GB   |
| `/usr/local/lib/node_modules` | Node.js modules                      | 0.50 GB |
| `/usr/local/share/chromium`   | Chromium                             | 0.61 GB |
| `/usr/local/share/powershell` | PowerShell                           | 1.3 GB  |
| `/usr/share/az_12.5.0`        | Microsoft Azure CLI                  | 0.50 GB |
| `/usr/share/dotnet`           | .NET                                 | 4.0 GB  |
| `/usr/share/miniconda`        | Miniconda                            | 0.80 GB |
| `/usr/share/swift`            | Swift                                | 3.2 GB  |

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

Files are removed using `rm -rf`, with `xargs` being used to remove directories
in parallel. We don't use `apt` to remove packages because that's slower.

This likely does break packages whose files are removed. If you try to install a
package that depends on one of these packages after running this action, it will
probably fail.

### What prior work was this based on?

This was inspired by [jlumbroso/free-disk-space] and
[ublue-os/remove-unwanted-software].

[jlumbroso/free-disk-space]: https://github.com/jlumbroso/free-disk-space
[ublue-os/remove-unwanted-software]:
  https://github.com/ublue-os/remove-unwanted-software
