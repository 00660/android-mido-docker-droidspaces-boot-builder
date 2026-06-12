# mido Docker / Droidspaces / virtual mic boot builder

Standalone GitHub Actions builder for Xiaomi Redmi Note 4/4X `mido`.

The workflow downloads the matching crDroid mido ROM, extracts `boot.img`, builds the `AlphaDroid-devices/kernel_xiaomi_mido` kernel with Docker / Droidspaces / ALSA loopback options, repacks boot, patches a second boot image with the latest official Magisk release, replaces the ROM zip's `boot.img`, and publishes only the final files to GitHub Releases.

## Defaults

- ROM: `crDroidAndroid-15.0-20260531-mido-v11.16.zip`
- ROM URL: `https://sourceforge.net/projects/kamisroms/files/Mido/Crdroid/A15/crDroidAndroid-15.0-20260531-mido-v11.16.zip/download`
- ROM SHA256: `E6DFF82977C04093379EB74FCBCD2891D0E6678CE4E54C13FDE2B773D6781CC3`
- Kernel repo: `https://github.com/AlphaDroid-devices/kernel_xiaomi_mido.git`
- Kernel ref: `alpha-14`
- Defconfig: `mido_defconfig`
- Release tag: `mido-crdroid-15-20260531-docker-droidspaces-audio-boot`

## Build

Run `.github/workflows/build-mido.yml` manually.

The workflow does not upload Actions artifacts. Successful output is published to the stable Release tag above and older assets under that tag are replaced.

Release assets include:

- integrated flashable ROM zip with Magisk pre-patched Docker / Droidspaces / virtual-audio boot
- plain Docker / Droidspaces / virtual-audio `boot.img`
- Magisk pre-patched `boot.img`
- final kernel `.config`
- build manifest
- official Magisk APK used for the patch

## Audio requirement

Kernel config includes:

```text
CONFIG_SOUND=y
CONFIG_SND=y
CONFIG_SND_TIMER=y
CONFIG_SND_PCM=y
CONFIG_SND_HWDEP=y
CONFIG_SND_RAWMIDI=y
CONFIG_SND_ALOOP=y
```

ROM/vendor still must expose loopback capture through Audio HAL and `audio_policy_configuration.xml` as an input profile/device. Kernel-only loopback creates ALSA PCM nodes, but WeChat / `AudioRecord` cannot use it until Android audio policy sees a capture route.

See `devices/mido/HANDOFF.md`.
