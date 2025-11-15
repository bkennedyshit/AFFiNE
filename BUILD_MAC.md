# Building AFFiNE Personal on macOS/Linux Mint

This guide covers building the custom AFFiNE fork with hardcoded AI endpoints on macOS or Linux Mint.

## Prerequisites

### Install Node.js LTS

```bash
# Using Homebrew (macOS)
brew install node@20

# Using apt (Linux Mint/Ubuntu)
sudo apt update
sudo apt install nodejs npm
```

Verify: `node --version` (should be 20.x or higher)

### Install Rust

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

Verify: `rustc --version`

### Install Yarn

```bash
corepack enable
corepack prepare yarn@stable --activate
```

Verify: `yarn --version` (should be 4.x)

## Clone the Repository

```bash
git clone https://github.com/bkennedyshit/affine-personal
cd affine-personal
```

## Build Steps

### 1. Install Dependencies

```bash
yarn install
```

This will install all npm dependencies and link workspace packages.

### 2. Initialize Workspace

```bash
yarn affine init
```

This generates necessary configuration files and TypeScript references.

### 3. Build Native Dependencies

```bash
yarn affine build -p @affine/native
```

This compiles the Rust native modules. This step takes a few minutes on first run.

### 4. Build Electron App

```bash
yarn affine build -p @affine/electron
```

This builds the desktop application with webpack/esbuild.

### 5. Package the App

```bash
cd packages/frontend/apps/electron
yarn make
```

This creates the distributable package (dmg for macOS, AppImage for Linux).

## Output

The built application will be in:

- **macOS**: `packages/frontend/apps/electron/out/make/` (look for `.dmg` file)
- **Linux**: `packages/frontend/apps/electron/out/make/` (look for `.AppImage` file)

## Running the App

### macOS

```bash
open packages/frontend/apps/electron/out/AFFiNE-0.25.2.dmg
```

### Linux

```bash
chmod +x packages/frontend/apps/electron/out/make/AFFiNE-0.25.2.AppImage
./packages/frontend/apps/electron/out/make/AFFiNE-0.25.2.AppImage
```

## Troubleshooting

### Build fails with "Cannot find module"

- Run `yarn install` again
- Run `yarn affine init` again

### Rust compilation errors

- Update Rust: `rustup update`
- Clean build: `rm -rf packages/frontend/native/target`

### Out of memory during build

- Increase Node memory: `export NODE_OPTIONS=--max-old-space-size=8192`

## Custom AI Configuration

The AI endpoints are hardcoded in the source. To modify them:

1. Find AI configuration in `packages/frontend/core/src/` (search for API endpoints)
2. Update the endpoints to point to your servers
3. Rebuild with `yarn affine build -p @affine/electron`

## Notes

- First build takes 10-15 minutes (native compilation)
- Subsequent builds are faster (5-10 minutes)
- The path fix from `personal apps/` to `personal_apps/` is already applied
- All AI features will use your custom servers instead of AFFiNE's
