# Using Inkscape to Extract Vector Paths from a PNG

## Introduction

Inkscape is a free, open-source vector graphics editor that can convert raster images (such as PNG files) into editable vector paths. This process is known as **bitmap tracing**.

The resulting vector paths can be edited, scaled without losing quality, and exported as SVG files for use in design, CAD, CNC, laser cutting, printing, or other vector-based workflows.

# 1. Open the PNG

1. Launch Inkscape.
2. Select **File → Open**.
3. Choose the PNG image.
4. Leave the import settings at their defaults and click **OK**.

# 2. Select the Image

Click once on the imported PNG.

Selection handles should appear around the image.

# 3. Open the Trace Bitmap Dialog

Navigate to:

**Path → Trace Bitmap**

or press:

```text
Shift + Alt + B
```

The **Trace Bitmap** dialog will appear.

# 4. Choose a Tracing Mode

## Single Scan

Recommended for:

* Logos
* Icons
* Silhouettes
* Black-and-white artwork

Available options include:

* **Brightness Cutoff**

  * Best general-purpose option.
* **Edge Detection**

  * Traces only the outlines.
* **Autotrace**

  * A good automatic tracing mode for many images.

For most logos, start with:

* Mode: **Brightness Cutoff**
* Threshold: **0.45–0.65**

Click **Preview** to inspect the result.

When satisfied, click **Apply**.

## Multiple Scans

Recommended for:

* Colored artwork
* Illustrations
* Photographs (limited usefulness)

Available modes include:

* Colors
* Grays
* Auto

Multiple scans create several overlapping vector paths representing different colors.

# 5. Separate the Vector from the PNG

After tracing, Inkscape places the newly created vector directly on top of the original PNG.

To verify:

1. Drag the object slightly.
2. You should see:

   * the original bitmap
   * the traced vector

Delete the bitmap if it is no longer needed.

# 6. Edit the Vector Path

Activate the **Node Tool**.

Shortcut:

```text
N
```

You can now:

* Move nodes
* Delete unnecessary nodes
* Adjust Bézier handles
* Smooth curves
* Refine corners

This is where most cleanup work is performed.

# 7. Simplify the Path

Automatic tracing often produces many more nodes than necessary.

To reduce complexity:

Select the path and choose:

**Path → Simplify**

Shortcut:

```text
Ctrl + L
```

Use this command carefully.

Each execution removes additional nodes and may alter the shape.

It is usually better to simplify once or twice rather than repeatedly.

# 8. Save the Vector

Choose:

**File → Save As**

Select:

```text
Plain SVG
```

This creates a clean, standards-compliant SVG suitable for:

* Web graphics
* Laser cutters
* CNC machines
* CAD software
* Other vector editors

# Example Workflow

1. Open a PNG.
2. Select the image.
3. Open **Path → Trace Bitmap**.
4. Choose **Brightness Cutoff**.
5. Preview the trace.
6. Click **Apply**.
7. Move the traced object away from the PNG.
8. Delete the original bitmap.
9. Edit the nodes.
10. Simplify if necessary.
11. Save as **Plain SVG**.

# Tips for Better Results

* Use high-resolution PNG images whenever possible.
* Transparent backgrounds generally produce cleaner traces.
* Logos and icons trace much better than photographs.
* Remove unwanted backgrounds before tracing.
* Avoid repeatedly simplifying paths, as excessive simplification can noticeably distort the artwork.
* Manual node editing often produces a significantly cleaner result than relying solely on automatic tracing.

# Keyboard Shortcuts

| Action        | Shortcut          |
| ------------- | ----------------- |
| Trace Bitmap  | `Shift + Alt + B` |
| Node Tool     | `N`               |
| Simplify Path | `Ctrl + L`        |

# Recommended Workflow

```
PNG
 │
 ▼
Open in Inkscape
 │
 ▼
Select Image
 │
 ▼
Path → Trace Bitmap
 │
 ▼
Preview
 │
 ▼
Apply
 │
 ▼
Move traced vector away
 │
 ▼
Delete original PNG
 │
 ▼
Edit Nodes
 │
 ▼
Simplify (optional)
 │
 ▼
Save as Plain SVG
```

# When to Use Each Tracing Mode

| Image Type           | Recommended Mode                                         |
| -------------------- | -------------------------------------------------------- |
| Black logo           | Brightness Cutoff                                        |
| Icon                 | Brightness Cutoff                                        |
| Silhouette           | Edge Detection                                           |
| Line drawing         | Edge Detection                                           |
| Colored illustration | Multiple Scans                                           |
| Photograph           | Multiple Scans (results may require significant cleanup) |
