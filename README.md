# BD Mod Manager

BD Mod Manager is a Windows app for Euro Truck Simulator 2 and American Truck Simulator, made by BD105. It installs mods from the BD Mods catalog into each game's mod folder, keeps a library of what is already installed, and lets you back up, restore, and clone game profiles while the game is closed.

## What the app does

- **Feed.** Browse ETS2 or ATS mods, then install, update, or remove one. The game tab you pick is remembered when you leave and come back.
- **Library.** See installed mods with their icons. Drop a `.scs` or `.zip` onto the box at the bottom to add it to the game tab that is open.
- **Profiles.** Read the installed game version, list the current profiles, and show the versioned backups the game already writes, such as `profiles(1.40).bak`. Back up the current set, replace it with an older backup, or clone one profile under a new name the game can open.
- **Settings.** Set the ETS2 and ATS mod folders, local-mod backups, and offline mode. A custom game home is used when the game was started with `-homedir`.

Close the game before a profile backup, restore, or clone. The game only reads its profile folders when it starts.

