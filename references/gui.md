# Fiji Plugin GUI Patterns

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
