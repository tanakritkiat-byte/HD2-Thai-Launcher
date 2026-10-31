# HELLDIVERS 2 Thai Localization - Pa-Kai

Source review snapshot for **HD2ThaiLauncher v30**, the Windows launcher for Pa-Kai's Thai localization mod.

This repository contains the project's launcher, translation compiler, local API text transformation, installer source, and build instructions. It is intended to make the executable's behavior inspectable for Nexus Mods reviewers and users.

## Download the source

The complete folder-preserving source snapshot is [HD2ThaiLauncher_v30_Source_Review.zip](HD2ThaiLauncher_v30_Source_Review.zip). Extract it and use the HD2-Thai-Launcher folder as the repository root for the build instructions below. The documents displayed here are copies of the same documents included in that archive.

## What it does

- Downloads readable Thai translations and validation rules from Pa-Kai's public Google Sheet.
- Validates placeholders, markup, subtitle timings and applicable byte limits before building translation patches. It applies Thai line-break opportunities using Windows ICU, followed by contextual Thai glyph encoding.
- Adds its own patch files to the game for the play session. A journal and file hashes control cleanup after the game exits; existing files owned by other mods are not overwritten.
- Starts the game's existing `start_protected_game.exe`. It does not patch the anti-cheat executable or bypass its startup.
- Runs a loopback proxy to translate selected live-server display text, such as Major Orders, news, campaigns and MOTD announcements.
- Requires the user's Windows confirmation to trust a locally generated certificate for that live-text function. Uninstallation invokes a certificate removal step.

Set the game's text language to **English (United States)** and start it through the mod launcher to use the translation.

## Review entry points

| Area | Source |
| --- | --- |
| Startup, local certificate, proxy and game launch | `Player_Launcher_v1/launcher.py` |
| Game discovery and folder selection | `game_discovery.py`, `launch_flow_ui.py` in the same folder |
| Patch deployment and cleanup | `Player_Launcher_v1/mod_session.py` |
| Main translation build | `Player_Launcher_v1/language_runtime.py`, `Translation_Updater/update_translation.py` |
| Live-text download and validation | `Player_Launcher_v1/api_rules.py` |
| API response display-field replacements | `Player_Launcher_v1/translation.py` |
| Local capture of MOTD display text | `Player_Launcher_v1/motd_capture.py` |
| Installer and uninstall behavior | `Player_Launcher_v1/build_installer.py` |

See [BUILD.md](BUILD.md) for build steps and [SECURITY_AND_NETWORK.md](SECURITY_AND_NETWORK.md) for certificate, network and local storage behavior.

## Relationship to the released executable

`SOURCE_PROVENANCE.json` identifies the v30 installer by SHA-256. The 21 project Python modules embedded in the launcher were compared with this snapshot as compiled Python code objects, with build-machine filenames normalized. All matched. The additional locked package-builder source was verified against its hash in the shipped runtime asset archive, for **22 verified source units** in total.

The portable build wrapper is new. Its installer script differs from the original only by allowing a portable Inno Setup compiler location. Original build scripts are retained in `original_build/` for comparison. Build tools, absolute paths, timestamps and the runtime's SSL environment can affect the binary output; a rebuild is **not claimed to be byte-for-byte identical** to the existing installer.

## Runtime assets

The 125 MB `language_assets.zip` is not committed to Git. It includes runtime font/resource data and reference inputs already shipped in the launcher. The build instructions explain how to provide the matching files from the existing distribution. Custom package-builder source is included here separately so it can be reviewed without that archive.

No player state, private certificate keys, authenticated browser exports or account credentials are included in this repository.

## Credits and rights

Project and translation: **Pa-Kai**. Third-party software notices are retained in `ThirdPartyNotices/`. Font/resource notices supplied with the runtime remain with those assets. This snapshot does not relicense third-party or game assets. No general open-source license for Pa-Kai's original code is granted by publishing this review snapshot.

This is a fan project and is not an official translation from Arrowhead Game Studios or Sony Interactive Entertainment.

