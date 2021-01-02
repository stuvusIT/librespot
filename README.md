# librespot

This role installs [librespot](https://github.com/librespot-org/librespot) and creates a
[systemd](https://wiki.debian.org/systemd) service for it.
That service can be configured to your needs, using the role variables documented below.

## Requirements

An apt based system like [Ubuntu](https://www.ubuntu.com/) or [Debian](https://www.debian.org/).

## Updating

This role can also update librespot to a desired version.
To do so, just change the [source specification](#source-specification) and the `librespot_version`
variable accordingly, and then reapply this role.

## Role Variables

| Name                  | Default                                                                 | Description                                       |
| :-------------------- | :---------------------------------------------------------------------- | :------------------------------------------------ |
| librespot_binary_url  | _undefined_                                                             | See [source specification](#source-specification) |
| librespot_archive_url | _undefined_                                                             | See [source specification](#source-specification) |
| librespot_github_repo | librespot-org/librespot                                                 | See [source specification](#source-specification) |
| librespot_checksum    | sha256:b8e99ac4bda359c0215eb6f666c1fe1520075d9b5557d39b889f595b5f07e45a | Checksum of the downloaded archive or binary      |
| librespot_version     | e8ec5feb2260e9d2e3b8bfc927cc5211d626e015                                | See [source specification](#source-specification) |
| librespot_user        | librespot                                                               | The user that runs librespot.                     |
| librespot_compile_dir | /tmp                                                                    | The base directory to use for compiling           |

## Role Variables Configuring Flags and Options

The following role variables configure
[librespot flags or options, documented here](https://github.com/librespot-org/librespot/wiki/Options).

| Name                                  | Default              | Description                                                 |
| :------------------------------------ | :------------------- | :---------------------------------------------------------- |
| global_cache_dir                      | _undefined_          | **[Must be set]** Local directory to store temporary files  |
| librespot_enable_cache                | True                 | Whether to pass `--cache "{{ librespot_cache_directory }}"` |
| librespot_verbose                     | False                | Whether to pass `--verbose`                                 |
| librespot_enable_volume_normalisation | True                 | Whether to pass `--enable-volume-normalisation`             |
| librespot_volume_ctrl                 | _undefined_          | If defined, argument passed to `--volume-ctrl`              |
| librespot_enable_discovery            | True                 | Whether **not** to pass `--disable-discovery`               |
| librespot_bitrate                     | 160                  | Argument passed to `--bitrate`                              |
| librespot_name                        | librespot            | Argument passed to `--name`                                 |
| librespot_cache_directory             | /var/cache/librespot | Argument passed to `--cache`                                |
| librespot_backend                     | alsa                 | Argument passed to `--backend`                              |
| librespot_initial_volume              | _undefined_          | If defined, argument passed to `--initial-volume`           |
| librespot_on_event                    | _undefined_          | If defined, argument passed to `--on-event`                 |
| librespot_username                    | _undefined_          | If defined, argument passed to `--username`                 |
| librespot_password                    | _undefined_          | If defined, argument passed to `--password`                 |
| librespot_proxy                       | _undefined_          | If defined, argument passed to `--proxy`                    |
| librespot_ap_port                     | _undefined_          | If defined, argument passed to `--ap-port`                  |
| librespot_device                      | _undefined_          | If defined, argument passed to `--device`                   |
| librespot_mixer                       | _undefined_          | If defined, argument passed to `--mixer`                    |
| librespot_zeroconf_port               | _undefined_          | If defined, argument passed to `--zeroconf-port`            |
| librespot_normalisation_pregain       | _undefined_          | If defined, argument passed to `--normalisation-pregain`    |
| librespot_extra_options               | []                   | List of strings (flags or options) passed to librespot      |

### Source Specification

There are multiple ways to specify the source from which librespot should be downloaded.
Chosing among them is done by providing exactly one of the following role variables.
Actually, multiple of those role variables can be provided, but then only the one listed here first
is considered.

* `librespot_binary_url`:
  Download a precompiled binary from that URL.
  **You should anyway set `librespot_version` to a value that's unique for that version.**
  This is because librespot is only re-downloaded, when the `librespot_version` role variable
  changes.
* `librespot_archive_url`:
  Build from source.
  The source is downloaded as an archive from that URL.
  The downloaded archive must contain a folder named `librespot-{{ librespot_version }}` which
  contains in turn the source that can be compiled with `cargo build`.
  The compilation yields a binary named `librespot` which is then installed.
* `librespot_github_repo`:
  Short form for
  `librespot_archive_url: https://github.com/{{ librespot_github_repo }}/archive/{{ librespot_version }}.tar.gz`.

## Example Playbook

The following playbook works if `~/.cache/stuvus` exists on the localhost.

```yml
- hosts: mpd01
  roles:
    - role: librespot
      global_cache_dir: "{{ lookup('env', 'HOME') }}/.cache/stuvus"

      librespot_name: stuvus-librespot
      librespot_user: stuvus-mpd
      librespot_bitrate: 320
      librespot_verbose: True
      librespot_backend: pulseaudio
```
