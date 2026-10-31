# Build instructions - Windows x64

## Requirements

1. Windows 10/11 x64 with `icu.dll` available in `System32`.
2. Python **3.12** x64, including Tcl/Tk. The existing v30 build used Python 3.12.14.
3. Python dependencies from `requirements-build.txt`. It records the versions installed in the v30 build environment; it is not a cryptographic dependency lock.
4. Inno Setup 6 for the optional Setup installer, with its Thai language file. Set `INNO_ISCC` if `ISCC.exe` is not in PATH or the usual installation location.
5. The matching runtime assets from the v30 launcher distribution.

## Obtain runtime data without running the game

From an extracted/installed v30 launcher distribution, locate:

```
HD2ThaiLauncher/
  HD2ThaiLauncher.exe
  _internal/
    language_assets.zip
    language_asset_manifest.json
```

Place those two data files in a local folder outside Git, for example `C:\HD2ReviewAssets\`. Do not provide any certificate, private key, cache or player state. The build wrapper checks the archive hash against `SOURCE_PROVENANCE.json`; for v30 it must be:

```
7cb9a99814d5f8a0aba718254713b57903fd85a8307ecbb50c71601846410027
```

The archive is omitted from this source repository because it contains font/game resource inputs and exceeds GitHub's normal per-file Git limit. The moderator can obtain it from the uploaded installer being reviewed. This build is therefore source-complete for the project's Python code, but requires the matching distributed runtime data; it is not a standalone recreation of proprietary game resources.

## Build the executable

From the repository root in PowerShell:

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-build.txt
.\.venv\Scripts\python.exe tools\build_public.py --assets-dir C:\HD2ReviewAssets
```

Output:

```
Builds/HD2_Thai_Launcher_v30_build/HD2ThaiLauncher/HD2ThaiLauncher.exe
```

The wrapper uses the already reviewed, checked-in runtime modules. It does not fetch a fresh Sheet, regenerate the locked baseline, or run the launcher/game as a build step. To avoid overwriting a previous build, the output directory must not already exist.

## Build the Setup installer

After the executable build:

```powershell
$env:INNO_ISCC = 'C:\Program Files (x86)\Inno Setup 6\ISCC.exe'
.\.venv\Scripts\python.exe Player_Launcher_v1\build_installer.py
```

Output: `Releases/Launcher/HD2ThaiLauncher_v30_Setup.exe`. This compiles the installer; it does not install it or open the game. The original installer script before the portable compiler-path adaptation is in `original_build/build_installer.py`.

## Safe local checks

```powershell
.\.venv\Scripts\python.exe tools\check_sources.py
.\.venv\Scripts\python.exe -m unittest discover -s tests -v
```

These selected tests operate on mocks and disposable test directories. They do not launch the game, install certificates, or intercept traffic. Running the launcher normally is different: it performs the actions described in `SECURITY_AND_NETWORK.md`.

## Reproducibility limits

The checked-in runtime sources match the v30 release's project code. The new build wrapper is designed for a portable checkout; build tools, embedded paths, timestamps, Python distribution and TLS policy data can change executable hashes. A successful build does not demonstrate in-game correctness, Nexus approval, or a clean antivirus result. No byte-identical rebuild is claimed.
