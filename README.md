# BD Mod Manager

BD Mod Manager is a Windows app for Euro Truck Simulator 2 and American Truck Simulator, made by BD105. It installs mods from the BD Mods catalog into each game's mod folder, keeps a library of what is already installed, and lets you back up, restore, and clone game profiles while the game is closed.

## What the app does

- **Feed.** Browse ETS2 or ATS mods, then install, update, or remove one. The game tab you pick is remembered when you leave and come back.
- **Library.** See installed mods with their icons. Drop a `.scs` or `.zip` onto the box at the bottom to add it to the game tab that is open.
- **Profiles.** Read the installed game version, list the current profiles, and show the versioned backups the game already writes, such as `profiles(1.40).bak`. Back up the current set, replace it with an older backup, or clone one profile under a new name the game can open.
- **Settings.** Set the ETS2 and ATS mod folders, local-mod backups, and offline mode. A custom game home is used when the game was started with `-homedir`.

Close the game before a profile backup, restore, or clone. The game only reads its profile folders when it starts.

The pieces behind the app:

- The **website** is the catalog and the admin desk where mods are published.
- The **API** chooses a file for the player's game, resolves dependencies, and checks downloads with SHA-256.
- The **desktop app** in `client-hybrid` is the Windows program. An older native client lives in `client`.

If you are continuing this project on another machine, start with [HANDOFF.md](HANDOFF.md).

## Run the site

Docker Desktop has to be running.

```powershell
copy .env.example .env
python scripts/make_admin_auth.py
docker compose up --build
```

`make_admin_auth.py` prints the username and password that guard the Admin Desk. Save the password, it is shown once. Run it again any time to change it.

Then open [http://localhost:2072](http://localhost:2072).

| Service | URL |
| --- | --- |
| Public catalog | http://localhost:2072 |
| API docs | http://localhost:2072/api/docs |
| Admin Desk | https://localhost:2073/admin (password prompt, then the admin sign-in) |

The Admin Desk port listens on this machine only. On the VPS, add `ADMIN_BIND=0.0.0.0` to `.env` to reach it from anywhere: `https://<vps-address>:2073/admin`. It uses a self-signed certificate, so the browser shows a warning the first time. Accept it once.
| MinIO console | http://localhost:9001 |

The first startup creates an admin from `ADMIN_EMAIL` and `ADMIN_PASSWORD` in `.env`. Change both, and `SECRET_KEY`, before anyone else can reach the machine.

Publish a mod under **Admin**: create the mod, upload a `.scs` or `.zip`, and set the game versions it supports. The mod page's **Install via BD Mod Manager** button uses the `bdmods://` link the desktop app registers.

## Run the desktop app

Install Rust (https://rustup.rs) and Node.js, then from `client-hybrid/src-tauri`:

```powershell
npm --prefix ../ui install
cargo run
```

On first launch, open **Settings** and confirm the mod folders. By default those are `Documents\Euro Truck Simulator 2\mod` and `Documents\American Truck Simulator\mod`. A custom `-homedir` path uses `<that path>\mod`.

Auto-update installs a newer catalog file unless that mod is pinned. Close the game before an install or a profile change. The game reads the mod folder and the profile folders only when it starts.

## Tests

The API flow (upload, signed download, dependency install plan, pinned and game-version filtering, and the publish WebSocket) is covered without Docker:

```powershell
cd server
python -m venv .venv
.\.venv\Scripts\python -m pip install -e ".[dev]"
.\.venv\Scripts\python -m pytest
```

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for how the pieces talk to each other.
