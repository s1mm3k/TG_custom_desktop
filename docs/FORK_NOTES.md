# Fork developer notes

Developer-facing notes for this fork of Telegram Desktop: what was changed, where,
and how to build and maintain it. All changes are client-side.

## Features and where they live

1. **Bypass protected content** — the per-type `allowsForwarding()` getters are
   kept honest, but the `PeerData::allowsForwarding()` dispatcher
   (`data/data_peer.cpp`), `HistoryItem::forbidsForward()`
   (`history/history_item.cpp`) and `Story::forbidsForward()`
   (`data/data_story.cpp`) are overridden to be permissive, so copy / save /
   screenshot stay available. A real server forward from a protected chat is
   impossible (`CHAT_FORWARDS_RESTRICTED`); only the client-side restrictions
   are lifted.

2. **Repost from protected chats** — in `ShareBox::DefaultForwardCallback`
   (`boxes/share_box.cpp`) and `ApiWrap::forwardMessages` (`apiwrap.cpp`): when
   the source really forbids forwarding, each item is re-sent as a new message
   (`SendExistingDocument` / `SendExistingPhoto` / `sendMessage`) instead of
   forwarded, so protected content can still be moved to another chat (no
   "forwarded from"). Stage 1 is a by-reference resend; a download+reupload
   fallback for protected media is a future stage.

3. **Full stealth mode**
   - no read receipts — `Histories::sendReadRequest` (`data/data_histories.cpp`)
     advances the local read state only, no network request;
   - no typing / activity — `SendProgressManager::send`
     (`api/api_send_progress.cpp`) returns early;
   - always offline — `Updates::updateOnline` (`api/api_updates.cpp`) forces the
     reported state to offline;
   - anonymous story viewing — `Stories::markAsRead` (`data/data_stories.cpp`)
     updates the local read position only, never calls `readStories` /
     `incrementStoryViews`.

4. **Anti-deletion** — `HistoryItem` gains a `_serverSideDeleted` flag and
   `markServerSideDeleted()` (`history/history_item.h/.cpp`);
   `Session::processMessagesDeleted` / `processNonChannelMessagesDeleted`
   (`data/data_session.cpp`) mark items instead of destroying them; the bubble
   is tinted red in `Message::paintText` / `Message::draw`
   (`history/view/history_view_message.cpp`). Note: this currently lives only in
   memory and is lost on restart (persistence is a separate task).

5. **Edit history (git-style diff)** — the text before an edit is kept in
   `HistoryItem::_previousVersionText`, captured in `applyEdition` before
   `setText`. The view builds a combined old+new text in `Element::validateText`
   (`history/view/history_view_element.cpp`) and paints red/green line
   backgrounds in `Message::paintText`. The "edited" badge position relies on
   the normal info skip-block (a forced text rebuild that dropped it was removed).

6. **Peer & DC ID** — numeric peer ID and the data-center of the profile photo
   are shown as rows in `info/profile/info_profile_actions.cpp`.

7. **Disable auto-download & animated stickers** — `Full::shouldDownload`
   (`data/data_auto_download.cpp`) returns false, and
   `Sticker::readyToDrawAnimationFrame` (`history/view/media/history_view_sticker.cpp`)
   never starts the animation player.

## Deferred

- **Persist deleted messages across restart.** Planned approach: store on
  delete, encrypted with the account local key (same mechanism as drafts:
  `EncryptedDescriptor` + `FileWriteDescriptor::writeEncrypted`). The hard part
  is re-injecting messages into the ordered history on load (needs MTPMessage
  reconstruction and hooking `History::addOlderSlice/addNewerSlice`); a separate
  "Deleted" panel is a simpler alternative.

## Building (Windows)

Requirements: Visual Studio 2026 (C++ desktop workload) with the MSVC v143
(14.44) toolset and the Windows 10 SDK 10.0.26100, plus Python 3.10 and Git.

```
git clone --recursive <this repo>
cd <repo>/Telegram
build\prepare\win.bat
configure.bat x64 -D TDESKTOP_API_ID=YOUR_ID -D TDESKTOP_API_HASH=YOUR_HASH ^
  -D CMAKE_CONFIGURATION_TYPES=Release ^
  -D DESKTOP_APP_DISABLE_AUTOUPDATE=ON -D DESKTOP_APP_DISABLE_CRASH_REPORTS=ON
cmake --build ..\out --config Release --target Telegram --parallel
```

Use your own `api_id` / `api_hash` from my.telegram.org. `win.bat` builds the
third-party dependencies once; afterwards only `cmake --build` is needed for
incremental builds.

## Branches and upstream

- `main` — default, release branch (clean history).
- `dev` — development and upstream integration (full history).

Pull upstream fixes on `dev`:

```
git fetch upstream
git checkout dev && git merge upstream/dev   # resolve conflicts, test
```

Then promote the ready state to `main`:

```
git checkout main && git checkout dev -- . && git add -A && git commit -m "Release vX.Y.Z" && git push origin main
```
