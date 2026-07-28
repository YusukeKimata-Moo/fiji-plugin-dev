# fiji-plugin-dev

A skill for Claude Code that helps develop, build, and deploy Fiji/ImageJ plugins in Java: writing `PlugIn`/`PlugInFilter` classes, setting up `plugins.config` and a build script, bundling external JARs into a fat jar, building Swing GUIs, and deploying the compiled JAR to a local Fiji install.

## Installation

### Global (available in every project)

```bash
mkdir -p ~/.claude/skills
cp -r fiji-plugin-dev ~/.claude/skills/
```

### Project-local (this project only)

```bash
mkdir -p .claude/skills
cp -r fiji-plugin-dev .claude/skills/
```

## Usage

Ask Claude Code to write, build, or debug a Fiji/ImageJ plugin, e.g.:

```
Write a Fiji plugin that measures the mean intensity of the active image
and logs it, then set up a build script for it.
```

The skill triggers on cues like "ImageJ plugin", "Fiji plugin", "PlugIn interface", "plugins.config", "build.sh for Fiji", and "fat jar for Fiji".

### First-time Fiji path setup

Fiji's install location is different on every machine, so it's never hardcoded. The build script looks it up from `references/fiji_path.txt`:

- If that file already contains a valid path, builds just work.
- If it's empty (the default, e.g. right after installing this skill), Claude will ask once for the local Fiji install location and save it there, so later builds on this machine don't need to ask again.

## What's Included

- **Plugin structure** — standard layout (`src/`, `plugins.config`, `build.sh`, output JAR) and the `plugins.config` menu-registration format.
- **Entry points** — `PlugIn`, `PlugInFilter` (requires an open image), `PlugInFrame` (window-based).
- **Build script pattern** — compiles against Fiji's own `ij-*.jar`, packages a JAR, and handles the Windows (`;`) vs. macOS/Linux (`:`) classpath separator.
- **Fat JAR pattern** — bundling dependencies that aren't already shipped inside Fiji's `jars/` folder.
- **GUI patterns** — non-modal Swing dialogs, ImageJ's built-in `GenericDialog`, and running long-running work on a background thread so Fiji stays responsive.
- **Common APIs** — a quick-reference table (`IJ.log`, `IJ.error`, `IJ.showProgress`, `IJ.getImage`) plus core classes (`ImagePlus`, `ImageStack`, `ImageProcessor`).
- **Deploy steps** — build, copy the JAR into `Fiji/plugins/`, restart Fiji.
- `references/fiji_api_tips.md` — additional API patterns, loaded only when needed.

## Folder Structure

```
fiji-plugin-dev/
├── SKILL.md                    # main skill instructions
├── references/
│   ├── fiji_api_tips.md        # extra API patterns (loaded on demand)
│   └── fiji_path.txt           # this machine's Fiji install path
└── README.md                   # this file
```

## License

MIT
