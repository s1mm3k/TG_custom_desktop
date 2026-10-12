# Custom Telegram Desktop (Windows 10/11 x64/x86 only)

A modified build of [Telegram Desktop][tdesktop] with extended control and
inspection features, intended for research workflows (OSINT / forensics).

This fork is based on the official [tdesktop][tdesktop] source and keeps its
MTProto protocol and [Telegram API][telegram_api] integration. All changes are
client-side modifications to the user's own client behaviour.

The source code is published under GPLv3 with OpenSSL exception, the same as
upstream; the license is available [here][license].

## Platform support

Official builds target **Windows 10 and Windows 11 only**, both **x64** and
**x86**. There are no prebuilt Linux or macOS binaries. Those platforms are not
supported here — if you need the client on Linux or macOS, build it yourself
from source using the upstream build instructions below.

* [Windows (32-bit and 64-bit)][win]
* [macOS (build from source)][mac]
* [GNU/Linux using Docker (build from source)][linux]

## Roadmap

Planned and in-progress features:

* **Bypass Protected Content** — remove the restrictions on copying text,
  forwarding messages and saving media that chat owners impose via the
  `noforward` flag.
* **Full Stealth Mode** — fully suppress every activity signal sent to the
  server: no "read" receipts, no "typing..." status, anonymous story viewing,
  and cancellation of any other outgoing activity reporting.
* **Anti-deletion** — keep messages deleted by the sender locally and mark them
  with a red background instead of removing them.
* **Peer & DC ID Display** — show the numeric peer IDs of users and chats and
  the data-center IDs of the servers.
* **Disable media auto-download & animated stickers** — turn off automatic media
  downloading and animated-sticker playback.

## Third-party

This fork inherits all upstream third-party components and their licenses; see
the upstream project for the full list. Notable ones include Qt 6 / Qt 5.15
(LGPL), OpenSSL 3 (Apache 2.0), WebRTC (BSD), FFmpeg (LGPL) and Opus (BSD).

[//]: # (LINKS)
[tdesktop]: https://github.com/telegramdesktop/tdesktop
[telegram_api]: https://core.telegram.org
[license]: LICENSE
[win]: docs/building-win.md
[mac]: docs/building-mac.md
[linux]: docs/building-linux.md
