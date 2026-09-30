# Fiji Plugin Build Recipes

Read when creating or changing a plugin build script. Resolve the installation path as described in the skill entrypoint. The recipes below use Bash; adapt them to the project's shell and dependency layout.

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

## Fat JAR (bundling non-standard dependencies)

If a dependency is NOT in Fiji's `jars/`, bundle it:

```bash
cd build && jar xf "$EXTERNAL_JAR" && cd ..
cd build && jar cf ../MyPlugin.jar plugins.config *.class org/ && cd ..
```

Common Fiji-bundled JARs (do NOT bundle):
- `ij-*.jar`, `commons-math3-*.jar`, `imglib2-*.jar`
