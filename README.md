# RetroOS updates

Update feed for RetroOS. The **System updates** page in Retro Shell Settings reads it.

| Device | Manifest |
| --- | --- |
| AYN Odin 2 Portal | [`odin2portal.json`](odin2portal.json) |
| AYN Odin 2 | [`odin2.json`](odin2.json) |

Devices fetch `https://raw.githubusercontent.com/TeamBrainStorm/retroos_updates/retroos/<device>.json`. They offer the newest entry whose `datetime` is later than their own build (`ro.build.date.utc`). The OTA zips are attached to this repository's [Releases](../../releases).

## Manifest format

The format follows the LineageOS updater, with a changelog added:

```json
{
  "response": [
    {
      "datetime": 1791541282,
      "filename": "RetroOS-1.0-20261009-v20-odin2portal.zip",
      "id": "<sha256 of the zip>",
      "romtype": "RELEASE",
      "size": 1441270687,
      "url": "https://github.com/TeamBrainStorm/retroos_updates/releases/download/<tag>/<filename>",
      "version": "1.0-v20",
      "changelog": "- One change per line"
    }
  ]
}
```

- `datetime` is the build's `ro.build.date.utc`, which is the same as the OTA metadata `post-timestamp`.
- `id` is the zip's sha256. The device checks it after downloading.

## How a device installs an update

1. It downloads the zip. Downloads resume, and an optional setting limits them to Wi-Fi.
2. It checks the sha256 against `id`.
3. It checks Android's OTA signature against the certificates already on the device (`/system/etc/security/otacerts.zip`).
4. It installs through `update_engine`, the A/B seamless update path, then asks for a restart.

Installing needs at least 30% battery or a connected charger.

## Publishing

Releases are created with `release-ota.sh` from the RetroOS scripts. It verifies the build, uploads the zip and its sha256 to a release, and updates the manifest with a fast-forward-only push. Nothing is published without a maintainer's approval.

For testing, a device can be pointed at another feed on userdebug builds:

```
adb shell setprop persist.retroos.updater.url http://host:port/
```

A URL ending in `/` gets `<device>.json` appended to it.
