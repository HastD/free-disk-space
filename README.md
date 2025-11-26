# Free disk space on Ubuntu GitHub Actions runner

This action frees disk space on Ubuntu runners.

Goals:

- Free as much disk space as possible by deleting tools that most workflows
  don't use.
- Run in under 2 minutes.
- Use defaults that work without further customization for most workflows.
- Give customization options to either exclude certain paths from deletion or
  include additional paths.

Inspired by [jlumbroso/free-disk-space] and [ublue-os/remove-unwanted-software].

[jlumbroso/free-disk-space]: https://github.com/jlumbroso/free-disk-space
[ublue-os/remove-unwanted-software]:
    https://github.com/ublue-os/remove-unwanted-software
