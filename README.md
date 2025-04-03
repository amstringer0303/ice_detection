# ❄️ Semi-Automated Detection of Landfast Ice Edge & Polynya Movement from Sentinel-2 Imagery

This project provides a **semi-automated method to detect the landfast ice edge and track polynya evolution** using Sentinel-2 imagery, training labels, and a coastline shapefile. The framework combines spectral, texture, and spatial features to classify ice vs. open water and extract meaningful ice-edge boundaries — applicable to *any* coastal Arctic scene, not just Wainwright.

---

## 🧠 What This Code Does

This workflow takes a single Sentinel-2 image and:

1. **Generates a Feature Stack**:
   - Normalized reflectance from Blue, Green, Red bands.
   - Spectral indices (Blue/Green, Green/Red, Blue/Red).
   - Texture features (brightness, edge intensity, local standard deviation).
   - Euclidean distance to the coastline (as a proxy for spatial context).

2. **Loads Vector Training Labels**:
   - A GeoJSON of manually labeled training polygons representing water and ice classes.

3. **Trains a Binary Random Forest Classifier**:
   - Labels are collapsed into two classes: open water (`0`) and ice (`1`).
   - Trained on extracted pixels from training regions.

4. **Performs Image-wide Prediction**:
   - Predicts water vs. ice across the entire scene.
   - Land areas are masked out using a vector coastline shapefile.

5. **Saves and Visualizes Results**:
   - Outputs a GeoTIFF of the binary classification.
   - Displays a classified map of open water vs. landfast ice.

---

## 🔍 Primary Output

- A **land-masked binary classification raster** from Sentinel-2 imagery:
  - `0 = Open Water` (typically polynyas)
  - `1 = Landfast Ice`
  - `NaN = Land`

- A **detected landfast ice edge**, derived from a user-supplied transect file:
  - Transects are drawn manually every 250 meters in QGIS or another GIS tool.
  - The script identifies the **first water pixel along each transect** (moving seaward from land).
  - A pixel is only considered part of the ice edge if it is located **at least 250 meters from the coastline**, ensuring more robust detection and avoiding nearshore misclassifications.

- These ice edge points can be exported or tracked across time-series imagery for change detection and polynya movement analysis.

---

## 📸 Example Outputs

Below is an example of what this method produces using Sentinel-2 imagery:

### RGB Sentinel-2 Image  
Shows the visual appearance of the ice, water, and land in natural color.

![RGB Image](figures/rgb_image_example.png)

### Ice/Water Classification with Detected Ice Edge  
The red line indicates the landfast ice edge automatically detected using the transect-based approach.

![Ice Edge Classification](figures/classification_map.png)

---

## 🌍 Input Requirements

1. **Sentinel-2 Image (3-band RGB)**  
   GeoTIFF containing at least Blue (B2), Green (B3), and Red (B4) bands.

2. **Training Polygons**  
   GeoJSON file with labeled polygon geometries and an `id` field for class.

3. **Coastline Shapefile**  
   Vector line shapefile representing the coastal boundary. Used for land masking and generating distance-to-coast features.

4. **Transect Shapefile (Manual Input)**  
   Manually created line features spaced every 250 meters. These should intersect the ice edge and originate from the coastline. Required for extracting the landfast ice edge.

---

## 🧪 Dependencies

Install required libraries via `pip`:

```bash
pip install rasterio numpy matplotlib geopandas shapely scikit-learn scipy scikit-image
