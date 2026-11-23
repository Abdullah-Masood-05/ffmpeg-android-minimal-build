# ffmpeg-android-minimal-build

[![build](https://github.com/Abdullah-Masood-05/ffmpeg-android-minimal-build/actions/workflows/build.yml/badge.svg)](https://github.com/Abdullah-Masood-05/ffmpeg-android-minimal-build/actions/workflows/build.yml)

A minimal **LGPL** FFmpeg 7.1.1 for **Android arm64-v8a** (API 24+), with LAME 3.100 and Opus 1.5.2 linked in statically. It is made for apps that run yt-dlp on the phone, such as [Keepsake](https://github.com/Abdullah-Masood-05/Keepsake). There it merges best video and best audio, remuxes and fixes up HLS/DASH, and extracts audio to MP3, M4A and Opus.

It is an **executable**, not a library. The `.so` name only makes Android unpack it into the app's `nativeLibraryDir`, where the app can run it.

The configure allow-lists mirror the desktop build in [ffmpeg-ytdl-minimal-build](https://github.com/Abdullah-Masood-05/ffmpeg-ytdl-minimal-build), adapted to the NDK.

## Download

Each [release](https://github.com/Abdullah-Masood-05/ffmpeg-android-minimal-build/releases) has:

| File | What it is |
|---|---|
| `libffmpeg-android-arm64-v8a.so` | The ffmpeg program for arm64 Android |
| `libffmpeg-android-sources.tar.gz` | The exact FFmpeg, LAME and Opus tarballs it was built from, plus `build.sh` and `sources.lock.json` (the LGPL "corresponding source") |
| `SHA256SUMS.txt` | SHA-256 of both files |

Pin a release tag and check the hash from `SHA256SUMS.txt` before bundling it.

In an Android app, put it at `src/main/jniLibs/arm64-v8a/libffmpeg.so` and set `android:extractNativeLibs="true"` or `useLegacyPackaging = true`. Then run it from `context.applicationInfo.nativeLibraryDir`.

## What the build checks

`build.sh` fails unless all of these hold:

- The output is an AArch64 PIE with interpreter `/system/bin/linker64`.
- Every LOAD segment is aligned to at least 16 KiB, so it works on 16 KiB-page devices.
- `DT_NEEDED` lists only Android system libraries (bionic, `libz`).
- The configured licence is LGPL 2.1+. There is no `--enable-gpl` and no `--enable-nonfree`.
- The `libmp3lame` and `libopus` encoders are enabled.

Every source tarball is pinned by SHA-256 in [`sources.lock.json`](sources.lock.json).

## Build it yourself

You need Linux x86_64 and Android NDK r27c (`27.2.12479018`), plus bash, curl, python3, make, pkg-config and xz.

```bash
export ANDROID_NDK_HOME=/path/to/android-sdk/ndk/27.2.12479018
bash build.sh
# -> dist/libffmpeg.so, plus cache/sources/ (corresponding source)
```

CI ([`.github/workflows/build.yml`](.github/workflows/build.yml)) builds on every push and pull request. A `v*` tag publishes a release.

## License

The build scripts are under the [LGPL-2.1](LICENSE).

| Component | License |
|---|---|
| FFmpeg | LGPL-2.1-or-later. Built without the GPL and non-free parts. |
| LAME | LGPL-2.0-or-later |
| Opus | BSD-3-Clause |

The full source used for each release is attached to that release.
