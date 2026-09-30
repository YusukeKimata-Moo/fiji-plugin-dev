---
name: fiji-plugin-dev
description: Develop, build, and deploy Java plugins for Fiji/ImageJ, including plugin GUIs, plugins.config, and JAR packaging. Use for plugin implementation; not routine image analysis, macro execution, or merely launching Fiji.
---

# Fiji/ImageJ Plugin Development

## Plugin Structure

```
project/
├── src/
│   ├── MyPlugin.java          # implements ij.plugin.PlugIn
│   └── AnotherPlugin.java
├── plugins.config              # menu registration
├── build.sh                    # compile + package
└── MyPlugin.jar                # output (goes to Fiji/plugins/)
```

### plugins.config

```
Plugins>MyMenu, "Command Name", ClassName
```

### Entry Point

```java
import ij.IJ;
import ij.plugin.PlugIn;

public class MyPlugin implements PlugIn {
    @Override
    public void run(String arg) {
        // Show GUI or process directly
    }
}
```

Other interfaces: `PlugInFilter` (requires open image), `PlugInFrame` (window-based).

## Build and packaging

Fiji's install location differs on every machine; do not hardcode it in reusable build instructions.

1. Check `references/fiji_path.txt` for a valid directory.
2. If absent or invalid, use a valid location already supplied by the user or environment instructions; otherwise ask once. Save the resolved location to `references/fiji_path.txt` for later builds.

Before creating or changing a build script, read [references/build.md](references/build.md) for the compile, package, and fat-JAR recipes. Classpath separators are `;` on Windows (including Git Bash) and `:` on macOS/Linux.

Compile against Fiji's own JARs. Bundle dependencies only when absent from Fiji's `jars/`; do not bundle Fiji-provided `ij-*.jar`, `commons-math3-*.jar`, or `imglib2-*.jar`.

## GUI patterns

Use a non-modal Swing window with `DISPOSE_ON_CLOSE` when Fiji must remain interactive, or ImageJ's `GenericDialog` for simple parameters. Handle dialog cancellation before reading values. Run long processing on a background thread.

Read [references/gui.md](references/gui.md) when implementing these GUI patterns.

## Common APIs

| Method | Purpose |
|---|---|
| `IJ.log(msg)` | Print to Log window |
| `IJ.error(msg)` | Show error dialog |
| `IJ.showProgress(i, total)` | Update progress bar |
| `IJ.getImage()` | Get active image |

Core classes: `ImagePlus`, `ImageStack`, `ImageProcessor`.

## Deploy

1. `bash build.sh`
2. Copy JAR to `Fiji/plugins/`
3. Restart Fiji

For image access, hyperstacks, ROI/overlays, file I/O, transforms, batch processing, or debugging, read [references/fiji_api_tips.md](references/fiji_api_tips.md).
