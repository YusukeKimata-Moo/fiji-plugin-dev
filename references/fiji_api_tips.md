# Fiji API Tips & Patterns

## Image Access

```java
// Get active image
ImagePlus imp = IJ.getImage();
ImageStack stack = imp.getStack();
int nSlices = stack.getSize();

// Access pixel data (32-bit float)
ImageProcessor ip = stack.getProcessor(sliceIndex);
float[] pixels = (float[]) ip.getPixels();

// Create new image
ImagePlus result = IJ.createHyperStack("Result", width, height, nChannels, nSlices, nFrames, bitDepth);
```

## Hyperstack Navigation

```java
// Dimensions: channel (c), slice (z), frame (t)
int[] dims = imp.getDimensions(); // [width, height, nChannels, nSlices, nFrames]
int index = imp.getStackIndex(c, z, t); // 1-based
imp.setPosition(c, z, t);
```

## ROI and Overlay

```java
import ij.gui.*;

// Point ROI
PointRoi roi = new PointRoi(xCoords, yCoords, nPoints);
imp.setRoi(roi);

// Overlay (non-destructive drawing)
Overlay ov = new Overlay();
ov.add(new Line(x1, y1, x2, y2));
imp.setOverlay(ov);
```

## File I/O

```java
// Open image
ImagePlus imp = IJ.openImage("/path/to/image.tif");

// Save image
IJ.saveAs(imp, "Tiff", "/path/to/output.tif");

// Open with Bio-Formats (for proprietary formats)
IJ.run("Bio-Formats Importer", "open=/path/to/file.nd2");
```

## Coordinate Transforms

```java
import ij.process.FHT;

// Fast Hartley Transform for cross-correlation
FHT fht1 = new FHT(ip1);
fht1.transform();
FHT fht2 = new FHT(ip2);
fht2.transform();
FHT result = fht1.conjugateMultiply(fht2);
result.inverseTransform();
```

## Batch Processing Pattern

```java
File[] files = dir.listFiles((d, name) -> name.endsWith(".txt"));
Arrays.sort(files);
for (int i = 0; i < files.length; i++) {
    IJ.showProgress(i, files.length);
    // process file...
}
IJ.showProgress(1.0); // complete
```

## Plugin Types Summary

| Interface | When to Use | `run()` Signature |
|---|---|---|
| `PlugIn` | No image required | `run(String arg)` |
| `PlugInFilter` | Requires active image | `setup(String, ImagePlus)` + `run(ImageProcessor)` |
| `PlugInFrame` | Creates own window | Constructor-based |

## Debugging

- `IJ.log()` outputs to the Log window (Window > Log)
- `IJ.showMessage("title", "msg")` for popup alerts
- Stack traces appear in Log window when using `e.printStackTrace()`
- Use `IJ.debugMode = true;` for verbose internal logging
