# Team Guide — Our Frigate Setup

This document is for anyone on the team, developer or not. It explains what's running,
how to use it day-to-day, and how to make changes safely.

## What this is

This is our working copy of **Frigate**, an open-source camera/security system that
watches video and automatically notices things like people and cars. Right now it's
running on [PC owner]'s computer with two test video feeds (not real cameras yet) so
we can safely learn and experiment before pointing it at anything real.

## Opening the dashboard

On the computer it's running on, open a browser to:

- **http://localhost:5000** (works immediately, no login)
- **https://localhost:8971** (secure version, needs login — browser will warn about the
  certificate, that's expected, click through)

**Login:** username `admin`, password `84a99d702a578dda4e8026002b5659ec`
*(This is a test password for a test setup. Please change it before this ever points
at real cameras, and don't commit real passwords to GitHub going forward — treat this
one as already "public" since it's written down here.)*

> **Important limit right now:** this only works *on that specific computer*. If someone
> else on the team wants to view this dashboard from their own machine, that's a separate
> "remote access" setup we haven't done yet — ask [PC owner] if that's needed.

## Everyday things anyone can do (no code, no file editing)

All of this is done by clicking around the dashboard itself:

- **Watch cameras live** — the main "Live" view, click a camera to enlarge it.
- **See what's been detected** — the "Review" tab shows Alerts and Detections as a
  scrollable timeline of thumbnails. Click any thumbnail to play that clip.
- **Adjust what counts as an alert** — Settings → Camera Settings → pick a camera →
  Review. You can change which objects (person, car, etc.) count as a high-priority
  "Alert" vs a lower-priority "Detection".
- **Draw zones / masks** — Settings → Camera Settings → Motion / Zones lets you draw
  directly on the video image to mark areas to watch closely or ignore. No file editing.
- **Add a new camera** — Settings → Cameras → Add Camera. If it's a real camera, it'll
  ask for its network address. If you want to add another test video, ask a developer
  (it needs a file placed on disk first).

Anything above is safe to click around — nothing here can "break" the system, and
changes take effect immediately.

## If you want to look at the actual config file

Advanced settings live in one file: `config/config.yml`. It's YAML, which means
**indentation matters** — two settings that look almost identical but are indented
differently can behave completely differently, and Frigate won't always warn you.
(We hit this exact bug ourselves — a setting got nested one level too deep and was
silently ignored for days before we noticed.) If you're not comfortable with YAML,
prefer the dashboard (above) over hand-editing this file.

## For actual code changes (developers)

See `CONTRIBUTING.md` for the full setup. Short version: this project has a
"Dev Container" already configured, which is the right way to edit and test code —
**not** the production Docker build we use to actually run the finished app (that one
takes several minutes to rebuild per change, far too slow for active editing).

1. Open the repo in VS Code with the "Dev Containers" extension → "Reopen in Container"
2. Frontend (website) changes: `cd web && npm run dev` — saves appear in ~1 second
3. Backend (Python) changes: edit the file, then restart Frigate inside the container
   to pick up the change

Team workflow: create a branch for your change, push it, open a Pull Request into
`dev` for review before merging. Don't push directly to `dev`.

## Common problems

- **"The dashboard won't load"** — the app runs in Docker containers that don't
  automatically restart when the computer restarts. Someone with terminal access needs
  to run: `docker start mqtt frigate`
- **"I changed a setting and nothing happened"** — check whether it saved correctly by
  reopening the settings page; if you edited `config.yml` by hand, double-check
  indentation (see above) — a misplaced setting fails silently rather than showing an error.
