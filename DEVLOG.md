# DEVLOG

## 2026-09-05 — Favorites and merged 1:1 threads match Messages again

Blip was online and polling, but two conversations diverged from Messages.app.

Favorites omitted a pinned group because Messages had re-keyed it. The pin
plist still named a retired chat row's `group_id`; `imsg chats` only matched
the live row, so that pin slot had no chat. The same clustering hole split a
1:1: Messages had merged a phone SMS row and an email iMessage row under one
`group_id`, Blip listed them as two threads, and the pin stayed on the stale
SMS handle.

`imsg` now clusters 1:1s that share `group_id`, matches pins against every
identifier in the cluster, and the Linux thread loader keeps alias rows.
Verified with unit tests (Python cluster/pin, TypeScript fold + selectThread)
and a live `imsg chats` check after deploying the Mac tool: the missing pin
slot resolves and the merged DM shows the newer iMessage timestamp.

Limitation: if the Mac itself has not received a message via Messages in
iCloud, Blip cannot invent it.
