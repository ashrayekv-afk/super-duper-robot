# NKPI bilateral calculator — 9.1.3

This release corrects a source of persistent corneal-map gaps: Map view previously used the incomplete 3D elevation mesh and therefore hid otherwise readable thickness and curvature values. **Map view now displays the full available report map in physical 2D x/y coordinates**, independently of missing surface heights. The natural-eye view and original source coordinates, point measurements, selected minimum, and calculator results are retained.

Other corrections retain supported three-corner triangles that the previous fixed grid diagonal omitted, estimate supported portions of narrow report-color gaps, and distinguish uncertain colors from unreadable samples. No example patient or patient report is embedded.
## Upload to GitHub

Extract **NKPI_v913_GitHub_Upload.zip** once. Upload these five files to the existing repository, replacing matching files.

| File | Location |
| --- | --- |
| `kcn_dual_calculator_app.py` | Repository root. This remains the Streamlit main file. |
| `nkpi_runtime.zip` | Repository root. **Keep this inner ZIP intact.** |
| `requirements.txt` | Repository root. |
| `README.md` | Repository root. |
| `.streamlit/config.toml` | Keep the `.streamlit` folder and this exact path. |

The four root files must be together, without another enclosing folder. Supporting application files are inside `nkpi_runtime.zip` and do not need individual upload. The launcher verifies the matching runtime and extracts it automatically.

In Streamlit, keep **Python 3.12** and **kcn_dual_calculator_app.py** as the main file. Reboot after the complete upload, refresh the page, and confirm **9.1.3-map-support** in the footer.

Open the original Pentacam ZIPs in the app again, verify the scalar values, and calculate. **Re-import the original reports after updating and verify the scalar values.** Imports from v9.1.2 already contain the dense map samples; older imported objects can use their existing calibrated grids but cannot acquire extra detail without the original report. Missing calibration is not guessed.

Local startup:

```bash
python -m pip install -r requirements.txt
python -m streamlit run kcn_dual_calculator_app.py
```

## Integrated cornea controls

- **OD and OS together:** linked rotation and zoom use equal physical axes and the same pixels per millimetre. The panels stack on narrow screens.
- **Whole surface / Section:** inspect the same surface or expose its available inner boundary. The section aligns with the direction of the lowest mapped thickness. It is a viewing cut, not tissue loss.
- **Map display:** natural appearance, anterior curvature, posterior curvature magnitude, thickness, anterior elevation, or posterior elevation. Quantitative colors are opaque and unlit, so the iris and lighting do not change their meaning. Legends show numbers, units and the selected value. Compatible eyes and visits share a stable scale; different definitions stay separate.
- **Map view:** opens the selected report map as a full 2D projection. A thickness or curvature sample remains visible even when its surface height is unavailable. The circle retains its own physical diameter and equal x/y scaling; it is not stretched onto a different surface. Click the map or use its coordinate sliders to inspect map samples. The point panel states when height is unavailable. From Natural eye it opens Thickness when available. 3D, Front, Profile, Whole surface, Section, or choosing Natural eye restores the surface view.
- **Selected point:** each eye and visit starts at its lowest positive, supported mapped thickness. X/Y controls let you inspect other locations; **Thinnest point** returns to the minimum. All five fields refer to that same location, and missing values stay unavailable. Manual selections are remembered separately for each eye and visit.
- **Estimate narrow gaps:** enabled initially. Small enclosed gaps and locally narrow portions of longer gaps may receive bounded estimates when direct original colors support both sides. Stripes mark estimates. Turn the checkbox off to remove them. Native missing values and unsupported surface heights are not completed. Source readings, the original selected minimum, and NKPI remain unchanged.
- **Map coverage:** Report support / Estimated / Color uncertain / Unavailable. The denominator is the full map circle in Map view and the retained surface in 3D. These percentages describe coverage, not accuracy. A full-map percentage can be lower while displaying more data because the sampling area is larger.
- **Baseline / Latest:** each visit uses its associated report. A missing eye or visit stays unavailable.
- **Save both eyes:** one PNG includes the views, selected values, visit, and source limitations.

The on-eye callout follows the selected measurement. Curvature shows diopters, elevation shows signed micrometres, and Thickness/Natural eye shows pachymetry. Only a supported thickness-derived boundary uses a front-to-inner caliper. Curvature and elevation use a point marker and label.

The synthetic eye option and observed-change/evidence section remain removed. Regional comparisons, source reports, and cone evolution remain in the collapsed comparison area below the integrated viewer.

## What the surface represents

| Source | Geometry and measurements |
| --- | --- |
| Native anterior/posterior coordinates | Retained in their declared physical frame. Device pachymetry remains independent of the vertical surface gap. |
| Report elevation and verified reference sphere | Approximate anterior geometry. Available pachymetry offsets an inner boundary along the reconstructed anterior normal. |
| Partial readable report | Supported 3D patches remain explicit. Its independently readable report maps can still be opened in Map view. |
| Report maps without usable height data | Full available 2D map values can be inspected. Patient surface geometry remains unavailable. Curvature and summary scalars never create an invented cone. |
| No associated scan | An import/linking message with no patient measurements. |

Image values are estimates. Intervals touching an accepted calibration endpoint can reflect an ambiguous color match or an endpoint color; they do not prove the device value exceeded the printed scale. Such samples remain excluded from exact values and surface heights. Their decoded central color may appear under a distinct crosshatch as an uncertain preview, never as an accepted numeric reading or interpolation donor. Original extracted grids remain unchanged for the existing regional calculations. Posterior elevation keeps its independent reference sphere and does not establish absolute posterior coordinates.

**Lowest reconstructed pachymetry** on a report-image model means the minimum among its retained, locally fitted source vertices. Native numerical data retain **Lowest mapped pachymetry**, the minimum among the supplied usable grid samples. Local fitting, color resolution and missing regions can make the report-derived minimum differ from the device's printed thinnest value. The scalar C is not used to invent a position or replace thickness at another coordinate. Missing pachymetry does not become zero. The lowest thickness point is not assumed to be the maximum curvature or maximum elevation point.

The detailed color map and the original reconstructed point grid are distinct. A detailed report-color sample may differ from a locally fitted point reading, and may include a smaller color-derived thickness value. The inspector identifies a different report-color sample separately. Switching to Map view alone preserves the original selected point and readings. Explicitly clicking a map location switches that eye’s table to map samples at that physical position, labels estimates, and withholds uncertain values. The Thinnest point button restores the original reconstructed/native minimum; it does not choose a new minimum from display estimates. Added display estimates never become a new reported minimum. Obtaining the device's numerical pachymetry and valid spatial coordinates is the route to a closer match to its supplied minimum.

If a readable minimum is isolated from the surrounding reconstructed surface, its point and value remain available with a surface-gap label. The viewer does not move to a thicker point or add unsupported tissue to make the caliper look complete.

Curvature colors progress from cooler lower values to warmer higher values. Thickness reverses that direction so thinner areas are warmer. Signed elevation uses blue for negative values, a neutral zero and orange for positive values. These colors encode measurements, not diagnostic categories. Contour bands represent displayed numerical levels, not validated cone boundaries.

A smaller reconstruction aperture is never stretched over a larger cornea. The iris, sclera, and unmeasured periphery are anatomical reference. Report images cannot establish an exact 1:1 whole-eye replica. Lighting does not simulate patient refraction or vision.

See `docs/SURFACE_DATA.md` inside the runtime for the coordinate contract, processing rules, native import format, and limitations.

## How gaps are handled

Fresh imports retain the previous 41 × 41 regional and 61 × 61 reconstruction grids. The new 121 × 121 map layer reads additional original image positions with the same accepted legend and color checks. It adds spatial display samples; it does not increase the precision of the printed color scale.

Quantitative colors use fine source-sample footprints instead of blanking an entire mesh triangle. Where a complete supported stencil exists, colors can interpolate between those samples. Otherwise the nearest supported sample retains only its own display footprint, and a missing sample retains its missing footprint. This presentation creates no additional source point measurements.

The gap estimator uses only original directly sampled neighboring colors, never its own earlier fills or local-color recoveries. Enclosed small components retain their existing checks. A narrow-band check now also considers local cross-width and opposing direct donors, so distant contact with the map edge does not disqualify every interior portion of a thin stripe. Actual unsupported rims, broad blank patches, nearby uncertain colors, inconsistent local fits and missing native numerical values remain unavailable. Accepted estimates are bounded by the surrounding original values. Engineering limits and connected holdout results are documented in `docs/SURFACE_DATA.md` and `docs/TEST_REPORT.md`.

For 3D topology, a cell with three original available corners can use their triangle when the old fixed diagonal would have omitted it. No new coordinates or thickness values are added, and no triangle spans multiple grid cells. This trims avoidable enlargement around some surface holes; it does not complete broad absent geometry.

Section removes a deliberate viewing wedge. Whole surface removes that viewing cut. Full Map view is a separate 2D projection of available XY data; it does not infer missing 3D heights. This release does not promise a completely filled or 1:1 measured cornea from report images.

## Numerical Pentacam data

The existing validated numerical surface/map route remains available. A compatible export must provide spatial coordinates, units, eye/visit, surface heights or correctly defined map values, and missing-data information. The included converter accepts explicitly documented pointwise CSV columns; it is not a universal Pentacam CSV, DICOM or proprietary-backup decoder.

See `docs/NUMERICAL_IMPORT.md` for the required data and association workflow. A sample of the clinic's actual numerical export is still needed to implement and validate any device-specific adapter. This version does not claim that such an adapter has been validated from JPG reports.

## Reliability and retained calculations

Trained models, coefficients, scalar calculations, bilateral import review, ranked input audit, and numerical/PDF exports are retained. Edited measurements, changed sources, eye removal, and next-patient clearing disconnect stale corneal data.

The matching **Tesseract.js 7.0.0** runtime, workers, supported LSTM cores, and English models are bundled. Original image ZIP OCR no longer needs a runtime download from a separate CDN. PDF support retains its existing loader requirements. Upstream licenses and checksums accompany the OCR assets.

The viewer adds no external texture service, API key, or JavaScript framework. Python declarations retain the tested `streamlit==1.50.0`, `reportlab==4.4.9`, and `numpy>=1.26.4,<3`. This release does not claim that every Python dependency is the newest published version.

See `docs/TEST_REPORT.md` for verification and its scope. Software checks establish implementation behavior, not clinical accuracy or surgical-planning validation.

Patient reports, extracted patient grids, photographs, and QA screenshots are excluded from this package.

## Bundle mismatch

Upload `kcn_dual_calculator_app.py` and `nkpi_runtime.zip` together from this same download, at the same repository level, preserving both names, then reboot. The launcher rejects a mismatched bundle instead of selecting another release.
