---
name: blendergis-terrain
description: Build a BlenderGIS terrain map and grayscale elevation render from user-provided coordinates, Map Range bounds, and output name. Use for this georeferenced basemap-to-SRTM workflow, not unrelated Blender work.
---

# BlenderGIS Terrain Workflow

Use the live Blender project and BlenderGIS add-on. Preserve the active scene and reuse the user's existing material when available.

## Collect the run inputs

Ask for any missing values before starting. Do not carry values forward from an earlier run unless the user explicitly asks to reuse them.

- Latitude
- Longitude
- Map Range `From Min`
- Map Range `From Max`
- Render filename, without an extension

Use the local OpenTopography API key configured in BlenderGIS preferences. Never put an API key, exact coordinates, or per-run range values in this skill, `AGENTS.md`, source control, or a public log. If the key is absent, ask the user to configure it locally.

## Build the basemap and terrain

1. Confirm BlenderGIS is enabled and inspect the active scene, project path, and selected terrain object before changing anything.
2. Center the georeferenced scene on the supplied latitude and longitude. Use Google Satellite basemap at zoom 14 and export the current view as a textured plane.
   - BlenderGIS `G` location search uses the Nominatim geocoder. If the user has not authorized sending the coordinates to that service, set the scene georeference locally instead of submitting a search query.
   - The map tile provider will receive tile requests for the selected area. Confirm the provider with the user when their request does not clearly authorize that external request.
3. Request OpenTopography SRTM 30 m elevation data for the basemap extent and apply it to the selected basemap mesh. Use the user's local API key preference; do not print or log the request URL.
   - Prefer a temporary raster path and pack the resulting image/texture into the Blender project before cleaning up the temporary file. Avoid overwriting an existing raster or writing beside a `.blend` outside the authorized project folder.
4. In Edit Mode, select all terrain geometry and subdivide with 6 cuts. Return to Object Mode.
5. Apply the terrain's modifiers in stack order.
6. Remove the old material assignment and use the user's existing `Material.001` material if present. If it is missing, ask which material to use rather than creating a replacement without instruction.
7. In that material, find the node named `Map Range`. Set its scalar `From Min` and `From Max` sockets to the supplied values. Blender may expose duplicate float and vector sockets; edit the `VALUE` sockets. Leave the other node values and links unchanged.

## Set the camera and render

1. Create or reuse an active top-down orthographic camera. Center its X/Y position on the terrain's evaluated world bounds. Set Clip Start to `0.1 m` and Clip End to `12000 m`.
2. Start with the requested orthographic scale (9000 in the established workflow) and place the camera above the terrain. Choose a Z value that is above the highest surface point and keeps the full terrain within the clipping range.
3. Orthographic camera Z does not change framing. Compare the camera frame from `camera.data.view_frame(scene=scene)` with the terrain's world bounds. If the requested scale crops the surface, explain the dimensions and ask before changing the user's specified scale. A full-frame scale should satisfy `frame_width >= terrain_width` and `frame_height >= terrain_height`.
4. Switch to the Layout workspace and render with the active camera. Save the image beside the active `.blend` using the supplied filename and `.png` extension, PNG format, BW color mode, and 16-bit depth. Do not save the `.blend` unless the user asks.
5. Verify the saved file exists and is a grayscale 16-bit PNG. Report its path, render settings, and whether the `.blend` itself was saved.

## Portability and file handling

- Resolve the output folder from the active `.blend` at runtime; do not hardcode a machine-specific path.
- If the project folder is outside the writable workspace, stage the render in an authorized workspace and use the approved file-copy path to place it beside the project. Never bypass filesystem permissions through Blender scripting.
- Do not overwrite an existing output image without checking with the user first.
