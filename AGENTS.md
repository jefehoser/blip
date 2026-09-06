# For AI agents working in this repository

You are probably here because a human asked you to install, fix, or extend
Blip. Read this first; it is short and every line has cost someone an evening.

## Non-negotiable

1. **Blip sends real iMessages to real people.** For any test that sends,
   the only acceptable target is the user's *own* number (their self-thread).
   Never a contact, never a group. Ask for the number if you do not have it.
2. **Never open, read, or quote a real conversation** to debug. Work from
   code, tests, and `bun collector.ts --deep | jq` shapes (metadata).
3. **Message text never touches disk on Linux and never rides argv** on
   either machine. If your change needs either, stop and redesign.
4. **No hostnames, user names, phone numbers, or home paths in code, tests,
   docs, or commit messages.** Use `+15551234567`, `you@your-mac`, `$HOME`.
5. **Logic in TypeScript with a test; QML only renders.** `bun test` must be
   green (CI enforces it). QML has no unit tests — screenshot UI changes
   with `grim` and look at the image.

## Where things are

- `CLAUDE.md` — architecture map and the invariants (read-marks, group
  sends, the hot-reload IPC zombie, scrolling, window lifecycle). Read it
  before editing `BarWidget.qml`, `BlipView.qml`, or `collector.ts`.
- `docs/SECURITY.md` — threat model and audit findings. `docs/PRIVACY.md` —
  what lands on disk.
- `bridge/mac/` — the Mac tools (Python, stdlib only). `bridge/linux/blip-shim`
  — the ssh shim. `scripts/blip-setup` — the wizard.
- `ROADMAP.md` — what is done, what is deferred and why.

## Danny's two commands

Danny is not a developer. He will say **update Blip** or **patch/fix Blip**.
Do the git/plugin/Mac work yourself. Report one or two sentences: what is
running now, and whether anything of his is still waiting on Nix. Do not
explain branches, clones, or the bridge unless he asks.

**Workshop** is `~/Work/blip`. **Installed Blip** is
`~/.config/omarchy/plugins/nixfred.blip`. **Mac tools** are `~/.blip/bin` on
the Mac named in `~/.config/blip/bridge.conf`. Omarchy runs the installed
copy, not the workshop.

### update Blip

1. In the workshop, fetch Nix (`upstream`). If a patch branch still exists
   and Nix has not merged it, rebase that branch onto current `upstream/main`
   and keep it. If Nix *has* merged it (or there is no patch), check out
   `main`, fast-forward to `upstream/main`, and delete leftover PR branches.
2. Run `omarchy plugin update nixfred.blip`.
3. If a patch is still unmerged, copy the workshop's `*.qml *.ts manifest.json`
   onto the installed copy so the update does not wipe Danny's fix.
4. If `bridge/` changed, re-run `scripts/blip-setup` with the host from
   `bridge.conf` (or `scp` the changed Mac tools to `~/.blip/bin`).
5. `omarchy-restart-shell`. Confirm with `qs … ipc call nixfred.blip status`.
6. Tell Danny the version now running. If a patch is still only on this
   machine, say so in one line.

### patch / fix Blip

1. Fetch Nix. Start a **new** branch from current `upstream/main`. Never
   continue an old PR branch.
2. Implement, `bun test`, deploy: copy `*.qml *.ts manifest.json` to the
   installed copy; `scp` any Mac-tool change to `~/.blip/bin`; then
   `omarchy-restart-shell`.
3. Ask whether to commit, push, and open a PR to Nix.
4. After Nix merges: do **update Blip** and delete the patch branch. The
   machine is back on Nix's path.

## Installing for a human

Follow README "Install" exactly. Stop at step 3 and wait for the human to
click the two macOS prompts; `blip-check` tells you when they have. Do not
try to grant TCC permissions programmatically — it is not possible, and
attempts trigger prompts on the Mac's screen.

## Deploying a change locally

```sh
bun test
cp *.qml *.ts manifest.json ~/.config/omarchy/plugins/nixfred.blip/
omarchy-restart-shell        # NOT a hot-reload: IPC would stay on the old instance
qs -p /usr/share/omarchy/shell ipc call nixfred.blip status
```

Mac-side tool changes: `scp bridge/mac/<tool> you@your-mac:~/.blip/bin/`.

## Commit messages

Subject under 72 chars; body says WHAT and WHY, and names any invariant you
touched. The log is the project's memory.
