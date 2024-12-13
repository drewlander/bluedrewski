# bluedrewski &nbsp; [![bluebuild build badge](https://github.com/drewlander/bluedrewski/actions/workflows/build.yml/badge.svg)](https://github.com/drewlander/bluedrewski/actions/workflows/build.yml)

See the [BlueBuild docs](https://blue-build.org/how-to/setup/) for quick setup instructions for setting up your own repository based on this template.

This is a custom blue build image for my personal use. Why use it? I don't know, but I use it and that's good enough for me.

What does it include?

* [Hardened Chromium from secureblue](https://github.com/secureblue/hardened-chromium) (non-flatpak)
* [Mullvad Browser](https://mullvad.net/en/download/browser/windows) (non-flatpak)
* My laptop has dual Intel and AMD, so packages for rocm for ollama etc. are installed
* codecs installed into base, as well as extra codecs. Flatpaks work too but hey, why not both? 
* Based on Ublue so things like 'ujust enroll-secure-boot-key' work
* Soon security hardening (kernel args, etc.). Secureblue is awesome, however too many things break my programs and I dont like SMP disabled. If I need security I will boot into Qubes or secureblue in a VM.

A full list is [here](https://github.com/drewlander/bluedrewski/blob/main/recipes/recipe.yml#L28)

Full list of flatpaks preinstall are [here](https://github.com/drewlander/bluedrewski/blob/main/recipes/recipe.yml#L63)

There is a bug where I had to create my own selinux module for virt-manager. It still works if you run:
```
fixfiles  -R libvirt-daemon-driver-qemu,libvirt-daemon-log restore && systemctl restart virtqemud virtlogd
restorecon -Rv /var/lib/libvirt
restorecon -Rv /var/log/libvirt
```

A few errros may come up but it boots fine. I have not been able to dig up why, I am trying to avoid an entire relabel.

Note: after rebasing, it takes a little bit for all of the flatpaks to download, they do not download ahead of time

## Installation

> **Warning**  
> [This is an experimental feature](https://www.fedoraproject.org/wiki/Changes/OstreeNativeContainerStable), try at your own discretion.

To rebase an existing atomic Fedora installation to the latest build:

- First rebase to the unsigned image, to get the proper signing keys and policies installed:
  ```
  rpm-ostree rebase ostree-unverified-registry:ghcr.io/drewlander/bluedrewski:latest
  ```
- Reboot to complete the rebase:
  ```
  systemctl reboot
  ```
- Then rebase to the signed image, like so:
  ```
  rpm-ostree rebase ostree-image-signed:docker://ghcr.io/drewlander/bluedrewski:latest
  ```
- Reboot again to complete the installation
  ```
  systemctl reboot
  ```

The `latest` tag will automatically point to the latest build. That build will still always use the Fedora version specified in `recipe.yml`, so you won't get accidentally updated to the next major version.

## ISO

If build on Fedora Atomic, you can generate an offline ISO with the instructions available [here](https://blue-build.org/learn/universal-blue/#fresh-install-from-an-iso). These ISOs cannot unfortunately be distributed on GitHub for free due to large sizes, so for public projects something else has to be used for hosting.

## Verification

These images are signed with [Sigstore](https://www.sigstore.dev/)'s [cosign](https://github.com/sigstore/cosign). You can verify the signature by downloading the `cosign.pub` file from this repo and running the following command:

```bash
cosign verify --key cosign.pub ghcr.io/drewlander/bluedrewski
```
