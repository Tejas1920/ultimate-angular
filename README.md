# Ultimate Angular

Modular (NgModule) Angular app packaged as a cross-platform desktop app with Electron.

**Stack:** Angular 21 · Electron 44 · electron-builder 26 · SCSS

**Targets:** Windows (32/64-bit) · Linux (x64, ARM64, ARMv7) · macOS (Intel, Apple Silicon, Universal)

---

## Prerequisites

- Node.js 22 LTS (20.19+ also works)
- npm 10+
- Git

```cmd
node -v
npm -v
```

---

## Quick Start

```cmd
git clone <your-repo-url>
cd ultimate-angular
npm install
```

---

## Run

| Goal                                        | Command                  |
| ------------------------------------------- | ------------------------ |
| Angular in browser (http://localhost:4200)  | `npm start`              |
| Electron with hot reload (development)      | `npm run electron:dev`   |
| Electron with production build (local test) | `npm run electron:start` |
| Angular production build only               | `npm run build:ng`       |

---

## Build Distributions

Output is created in the `release/` folder.

| Platform            | Command                      | Output                         |
| ------------------- | ---------------------------- | ------------------------------ |
| Windows 32-bit      | `npm run dist:win32`         | Installer + portable exe       |
| Windows 64-bit      | `npm run dist:win64`         | Installer + portable exe       |
| Windows 32 + 64-bit | `npm run dist:win`           | Both                           |
| Linux x64           | `npm run dist:linux64`       | `.AppImage`, `.deb`            |
| Linux ARM64         | `npm run dist:linux-arm64`   | `.AppImage`, `.deb`            |
| Linux ARMv7         | `npm run dist:linux-armv7l`  | `.AppImage`, `.deb`            |
| macOS Intel         | `npm run dist:mac-intel`     | `.dmg`                         |
| macOS Apple Silicon | `npm run dist:mac-silicon`   | `.dmg`                         |
| macOS Universal     | `npm run dist:mac-universal` | `.dmg` (Intel + Apple Silicon) |

> 32-bit Linux is not supported by Electron.

---

## Where Each Build Must Run

| Target  | Windows PC              | Linux / WSL2 / Docker | Mac     |
| ------- | ----------------------- | --------------------- | ------- |
| Windows | Yes                     | -                     | -       |
| Linux   | No (use WSL2 or Docker) | Yes                   | -       |
| macOS   | **No**                  | **No**                | **Yes** |

### Linux build from Windows (WSL2)

```cmd
wsl --install
```

Restart, then inside the Ubuntu terminal:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.bashrc
nvm install 22
cd ~
git clone <your-repo-url> ultimate-angular
cd ultimate-angular
npm ci
npm run dist:linux64
```

Clone into `~/`, not `/mnt/d/...` (slow, and reuses Windows `node_modules`).

### Linux build from Windows (Docker)

```cmd
docker run --rm -ti -v %cd%:/project -v ultimate-angular-node-modules:/project/node_modules -w /project electronuserland/builder:latest /bin/bash -c "npm ci && npm run dist:linux64"
```

### macOS build

Run on a Mac:

```bash
npm ci
npm run dist:mac-universal
```

---

## Build All Platforms with GitHub Actions

Workflow file: `.github/workflows/build.yml`

```cmd
git tag v1.0.0
git push --tags
```

Download the installers from the workflow run under **Actions → Artifacts**.

---

## Before Release

- Set `version`, `description`, `author` in `package.json`
- Add icons in `build/`: `icon.ico` (Windows), `icon.icns` (macOS), `icons/` PNG set (Linux)
- Windows: unsigned installers show a SmartScreen warning (code-signing certificate needed)
- macOS: unsigned `.dmg` needs right-click → Open (Apple Developer account needed for signing/notarization)

---

## Troubleshooting

**Blank window on `npm run electron:start`**

```cmd
dir dist\ultimate-angular\browser\index.html
findstr "base href" dist\ultimate-angular\browser\index.html
```

- `base href` must be `./`
- Hash routing must be enabled
- Dev mode must be controlled by the `ELECTRON_DEV` variable

**Clean reinstall**

```cmd
rmdir /s /q node_modules dist release
npm install
```

**macOS build fails on Windows / Linux**

Expected. Use a Mac or GitHub Actions.

**`.deb` build fails**

Build on Linux (WSL2, Docker or CI) and make sure `author` has an email in `package.json`.
