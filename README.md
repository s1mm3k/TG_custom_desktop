# Custom Telegram Desktop

A modified build of [Telegram Desktop][tdesktop] with extended **control,
privacy and convenience** features. Everything here is a client-side change to
your own client's behaviour — the same category of fork as AyuGram / 64Gram /
Kotatogram.

Built and distributed for **Windows 10 / 11 only** (x64 and x86). It keeps
upstream's MTProto protocol and [Telegram API][telegram_api] integration.

> ⚠️ Use responsibly and only where you are allowed to. These features change
> how *your* client behaves; they do not break Telegram's server-side
> encryption or the privacy of other users.

## Features

### 1. Bypass protected content
Removes the client-side restrictions imposed by the "Restrict saving content"
(`noforward`) flag: **copy text, save media, select and screenshot** stay
available in chats and channels that disabled them.

### 2. Repost from protected chats ("forward" that works)
A real server forward from a protected chat is rejected by Telegram
(`CHAT_FORWARDS_RESTRICTED`). Instead of failing, the fork **reposts** the
content as a new message (text, photo, video, document) — AyuForward-style,
without the "forwarded from" header.

### 3. Full stealth mode
Suppresses every outgoing activity signal:
* no **read receipts** (the sender never sees "read");
* no **typing / recording / uploading** indicators;
* always reported **offline** (no online pings);
* **anonymous story viewing** — you never appear in the viewer list or counts.

### 4. Anti-deletion
Messages deleted by the sender are **kept** instead of disappearing, and the
whole bubble is tinted **red** so they stand out.

### 5. Edit history (git-style diff)
When a message is edited, the previous version is shown **struck-through on a red
background** above the new version on a **green background** — like a git diff.

### 6. Peer & DC ID display
The profile of any user, chat or channel shows its numeric **ID** and the
**data-center (DC)** its profile photo is stored on.

### 7. Disable auto-download & animated stickers
Media (photos, videos, files, voice) is **never auto-downloaded** — it loads
only when you open it — GIFs/videos do not auto-play, and stickers and emoji are
rendered **statically** (first frame only).

## Download

Prebuilt Windows binaries are published on the [Releases][releases] page. The
build is portable — unpack and run `Telegram.exe` (it keeps its own profile and
does not touch an existing Telegram installation).

## Platform support

Official builds target **Windows 10 and Windows 11** only, both **x64** and
**x86**. There are no prebuilt Linux or macOS binaries — build from source with
the upstream instructions if you need them.

* [Windows (32-bit and 64-bit)][win]
* [macOS (build from source)][mac]
* [GNU/Linux using Docker (build from source)][linux]

## Building from source

Requirements (Windows): Visual Studio 2022/2026 with the C++ desktop workload
and the matching Windows 10 SDK (see [docs/building-win.md][win]), plus Python 3
and Git.

```
git clone --recursive https://github.com/s1mm3k/tdesktop_custom.git
cd tdesktop_custom\Telegram
build\prepare\win.bat
configure.bat x64 -D TDESKTOP_API_ID=YOUR_ID -D TDESKTOP_API_HASH=YOUR_HASH
cmake --build ..\out --config Release --target Telegram
```

You need your own `api_id` / `api_hash` from [my.telegram.org][api_credentials];
a modified client using the public test keys can hit `API_ID_PUBLISHED_FLOOD`.

## Third-party

This fork inherits all upstream third-party components and their licenses; see
the upstream project for the full list. Notable ones include Qt 6 / Qt 5.15
(LGPL), OpenSSL 3 (Apache 2.0), WebRTC (BSD), FFmpeg (LGPL) and Opus (BSD).

## License

Published under **GPLv3 with the OpenSSL exception**, the same as upstream; see
[LICENSE][license]. This is an unofficial fork and is not affiliated with or
endorsed by Telegram.

[//]: # (LINKS)
[tdesktop]: https://github.com/telegramdesktop/tdesktop
[telegram_api]: https://core.telegram.org
[api_credentials]: https://my.telegram.org
[releases]: https://github.com/s1mm3k/tdesktop_custom/releases
[win]: docs/building-win.md
[mac]: docs/building-mac.md
[linux]: docs/building-linux.md
[license]: LICENSE
