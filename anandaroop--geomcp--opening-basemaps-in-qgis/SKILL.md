---
name: opening-basemaps-in-qgis
description: Open vector basemap layers in QGIS for convenient visualization. Use this skill when the user wants "show that in qgis", "visualize those layers", etc. Use when this capability is needed.
metadata:
  author: anandaroop
---

# Opening Basemaps in QQIS

You will open and modify basemap layers in QGIS using the `qgis` MCP server

Workflow:

```
- [ ] Ensure QGIS project is ready
- [ ] Add layers to QGIS
- [ ] Show/hide layers appropriately
- [ ] Style layers appropriately
- [ ] Apply final highlighting
```

## Ensure QGIS project is ready

Ensure QGIS has a project open with no layers

Inform the user if:

- QGIS or the MCP server are not available
- Project is available but not empty

Instruct them to ensure an empty project is ready to go.

Once you have an empty project, proceed.

## Add layers to QGIS

If you know the recently generated layers, start adding them to QGIS.

If not, solicit the path for the folder that contains the layers to be visualized

They are typically shapefiles, especially if they were extracted from Natural Earth.

Add the layers in a sensible order so that features will be visible as expected.

First the physical layers, e.g. land > lakes > rivers

Then the cultural layers, e.g. admin0 > admin1 > populated places

## Show/hide layers appropriately

IMPORTANT: Ensure that ALL layers have been added, and the related layers appear next to each other in the stacking order.

But hide layers that would obscure the layers beneath them.

E.g. prefer to show `admin_0_boundary_lines_land` and `admin_0_boundary_lines_disputed_areas` (both of which contain line features), but hide `admin_0_countries` (which contains area features).

So the final order and visibility for a default set of layers might look like:

```
- [x] admin_1_states_provinces_lines
- [ ] admin_1_states_provinces_scale_rank
- [x] admin_0_boundary_lines_disputed_areas
- [x] admin_0_boundary_lines_land
- [ ] admin_0_countries
- [x] rivers_lake_centerlines_scale_ranl
- [x] lakes
- [x] land
```

Note that all layers have been added but not all are visible.

## Style layers appropriately

Use the template in `./templates/styles.py` to create a script that QGIS will execute

It contains styles for all default layers. If any additional layers are being added, style them appropriately.

Once you have done this, no need to proceed further. I.e. do not create a preview PNG.

The QGIS project itself is the end goal.

# Apply final highlighting

If a specific state or country was requested as the subject of the map then we can apply one final optimization.

Un-hide the relevant admin0 or admin1 _area_ layer and apply a filter so that the subject state/country is the only visible geometry.

Set it to have a red fill, but adjust its opacity to 10% to create a subtle highlight effect. Done, no need to zoom.

# Caveats

- **Do not try to reorder layers**. It seems that whenever you try you end up deleting all the layers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anandaroop) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
