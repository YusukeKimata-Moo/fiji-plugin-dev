---
name: fiji-plugin-dev
description: >
  Develop, build, and deploy Fiji/ImageJ plugins in Java.
  Use when: writing or modifying ImageJ plugin Java source, setting up build scripts
  for Fiji plugins, creating plugin GUIs with Swing, bundling external JARs (fat jar),
  configuring plugins.config, or deploying plugins to Fiji.
  Triggers: "ImageJ plugin", "Fiji plugin", "PlugIn interface", "plugins.config",
  "build.sh for Fiji", "fat jar for Fiji".
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

## Build Script Pattern

Fiji's install location differs on every machine, so never hardcode it. Instead:

1. Check `references/fiji_path.txt`. If it holds a valid directory, use it.
2. If it's empty or invalid, ask the user for their Fiji install location once, then save it to `references/fiji_path.txt` so later builds on this machine don't need to ask again.

```bash
FIJI_PATH_FILE="references/fiji_path.txt"
FIJI_DIR="${1:-$(cat "$FIJI_PATH_FILE" 2>/dev/null)}"

if [ -z "$FIJI_DIR" ] || [ ! -d "$FIJI_DIR" ]; then
  echo "Fiji install location not set. Write it to references/fiji_path.txt (one line)."
  exit 1
fi

IJ=$(ls "$FIJI_DIR"/jars/ij-*.jar 2>/dev/null | head -1)

javac -cp "${IJ};${OTHER_JARS}" -d build src/*.java
cp plugins.config build/
cd build && jar cf ../MyPlugin.jar plugins.config *.class && cd ..
```

On Windows (Git Bash), classpath separator is `;`. On macOS/Linux use `:`.

### Fat JAR (bundling non-standard dependencies)

If a dependency is NOT in Fiji's `jars/`, bundle it:

```bash
cd build && jar xf "$EXTERNAL_JAR" && cd ..
cd build && jar cf ../MyPlugin.jar plugins.config *.class org/ && cd ..
```

Common Fiji-bundled JARs (do NOT bundle):
- `ij-*.jar`, `commons-math3-*.jar`, `imglib2-*.jar`

## GUI Patterns

Swing GUI (non-modal, Fiji stays interactive):

```java
JFrame frame = new JFrame("My Plugin");
frame.setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE);
frame.setLayout(new GridBagLayout());
// Add components...
frame.pack();
frame.setLocationRelativeTo(null);
frame.setVisible(true);
```

Simple parameter dialog (ImageJ built-in):

```java
GenericDialog gd = new GenericDialog("Parameters");
gd.addNumericField("Window size", 65, 0);
gd.showDialog();
if (gd.wasCanceled()) return;
double val = gd.getNextNumber();
```

Run long processing on a background thread:

```java
new Thread(() -> processData()).start();
```

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

See `references/fiji_api_tips.md` for additional API patterns.
