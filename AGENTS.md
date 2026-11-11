# Project Agent Instructions

This project uses BlenderGIS for a georeferenced basemap, SRTM elevation surface, and grayscale render. For the full procedure, read [the BlenderGIS terrain skill](blendergis-terrain/SKILL.md).

For each run, get these values from the user instead of reusing prior-session values:

- Latitude and longitude
- Map Range `From Min` and `From Max`
- Render filename

Workflow conventions:

- Use Google Satellite at zoom 14 and OpenTopography SRTM 30 m when the user requests the established workflow.
- Use 6 subdivide cuts, apply the terrain modifiers, and use the user's existing `Material.001` when available.
- Set the scalar `Map Range` sockets from the supplied values; preserve other nodes and links.
- Use a top-down orthographic camera with Clip Start `0.1 m` and Clip End `12000 m`. Check actual frame fit; camera Z alone does not enlarge an orthographic frame.
- Render to PNG, BW, 16-bit using the user's output name, in the active `.blend` folder.
- Never store API keys, coordinates, Map Range values, or machine-specific paths in shared project instructions. Use the local BlenderGIS OpenTopography key preference.
- Get authorization for external map and elevation requests when the user's current request does not clearly specify the provider and data. Respect filesystem permissions; do not bypass them through Blender scripting.
- Leave the `.blend` unsaved unless the user asks to save it.
