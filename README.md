# SPOT Extension Specification

- **Title:** SPOT
- **Identifier:** <https://cnes.github.io/spot-stac-extension/v0.1.0/schema.json>
- **Field Name Prefix:** spot
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @emmanuelmathot

This document explains the SPOT Extension to the [SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.

The extension describes the scene metadata of the SPOT 1 to 5 satellites that no other STAC extension covers:
the position of a scene in its acquisition segment, the shift along the track, the sensor code, the coupled acquisitions
and the SPOT catalogue cloud notation. CNES uses it for the SPOT World Heritage archive in the
[GEODES](https://geodes.cnes.fr/) catalog.

- Examples:
  - [Item example](examples/item.json): A SPOT-2 panchromatic scene with a coupled multispectral scene
  - [Collection example](examples/collection.json): Summaries of the SPOT fields in a STAC Collection
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## Sources

The field definitions follow the SPOT product specifications. The documents are available on the
[SPOT World Heritage documentation page](https://regards.cnes.fr/user/swh/modules/54) of the CNES REGARDS portal:

- S-ST-73-02-SI, *Spécification de définition et de format des produits de base SPOT 1 à 5* (DIMAP)
- S4-ST-73-01-CN and S4-ST-73-01-SI, *The SPOT Standard Digital Product Format* (CAP/CEOS)
- S5-ST-73-01-CN, *Spécification de format des produits SPOT* (SPOT 5)
- S-NT-73-12-SI, *SPOT Geometry Handbook*
- SI/GP/86.0005 annex 1, *Grille de Référence SPOT* (GRS)

## Fields

The fields in the table below can be used in these parts of STAC documents:

- [ ] Catalogs
- [x] Collections (summaries only)
- [x] Item Properties (incl. Summaries in Collections)
- [ ] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections and Asset Templates)
- [x] Links (`spot:coupling_mode` only)
- [ ] Bands

| Field Name                | Type      | Description                                                                                   |
| ------------------------- | --------- | --------------------------------------------------------------------------------------------- |
| spot:sensor\_code         | string    | Sensor (acquisition mode) that acquired the scene, as DIMAP `SENSOR_CODE`.                    |
| spot:scene\_extent        | string    | `full` for a complete scene, `extract` for a scene cut to an area of interest.                |
| spot:shift\_value         | integer   | Shift Along the Track (SAT) relative to the GRS node, in tenths of a scene (0 to 9).          |
| spot:segment\_id          | string    | Identifier of the segment (GERALD name) that contains the scene.                              |
| spot:scene\_rank          | integer   | Position of the scene in its segment, from 1 to `spot:scene_count`.                           |
| spot:scene\_count         | integer   | Number of scenes in the segment.                                                              |
| spot:scene\_id            | string    | Compact SPOT scene key. It is the join key between coupled scenes.                            |
| spot:coupled\_mode        | boolean   | `true` if the scene was acquired at the same time as scenes in another mode.                  |
| spot:coupling\_modes      | \[string] | Mode combinations of the coupled acquisition, for example `PAN+XS`.                           |
| spot:cloud\_cover\_quotes | string    | SPOT catalogue cloud notation: 8 letters, one per sub-area, from `A` (clear) to `E` (cloudy). |

An Item that declares this extension must have at least one of these fields.

### Additional Field Information

#### spot:sensor\_code

The allowed values are `A`, `B`, `J`, `I`, `X`, `P`, `M` and `S` (HRS) for SPOT 1 to 5,
and `HMA`, `HMB`, `HX` and `SM` for the SPOT 5 THR (2.5 m) mode.

#### spot:shift\_value

A SPOT scene is either centred on a node of the SPOT Reference Grid (GRS), or shifted along the track by tenths of a scene.
The value 0 is a scene centred on the node. The GRS node itself goes in the
[Grid Extension](https://github.com/stac-extensions/grid) field `grid:code`, as `SPOTGRS-<K>-<J>` (for example `SPOTGRS-553-212`).

#### spot:segment\_id

A segment is one continuous acquisition by one instrument in one mode, received by one station.
It is the SPOT equivalent of a datatake. Example: `S5_G1_A_DT_200709301703559_CS_001952`
reads SPOT 5, HRG 1, sensor A, mode DT, segment start 2007-09-30 17:03:55.9, station CS, revolution 1952.

#### spot:cloud\_cover\_quotes

The SPOT catalogue gives one cloud note per sub-area of the scene (8 sub-areas), from `A` (clear) to `E` (cloudy),
for example `BDADBDBE`. Use `eo:cloud_cover` from the [EO Extension](https://github.com/stac-extensions/eo)
for the overall cloud percentage.

## Link fields

| Field Name          | Type   | Description                                                                                  |
| ------------------- | ------ | -------------------------------------------------------------------------------------------- |
| spot:coupling\_mode | string | Coupling mode of the linked scene (for example `px`, `thr`, `hmx`). Only on `related` links. |

Each coupled scene is a [Link Object](https://github.com/radiantearth/stac-spec/tree/master/item-spec/item-spec.md#link-object)
with `rel` set to `related`, which points to the Item of the coupled scene:

```json
{
  "rel": "related",
  "href": "https://example.com/items/S2_553-212-0_2007-09-30-17-54-17_HRV-1_X_DT_LM",
  "type": "application/geo+json",
  "spot:coupling_mode": "px"
}
```

## Fields from other extensions

A SPOT Item also uses these fields from other extensions:

| SPOT concept                            | STAC field                                                                                      |
| --------------------------------------- | ----------------------------------------------------------------------------------------------- |
| GRS node K-J                            | `grid:code` = `SPOTGRS-<K>-<J>` ([Grid](https://github.com/stac-extensions/grid))               |
| Instrument (HRV, HRVIR, HRG, HRS)       | `instruments`, for example `["hrv"]` (STAC common metadata)                                     |
| Receiving station                       | `sat:acquisition_station` ([SAT](https://github.com/stac-extensions/sat) v1.2.0)                |
| Incidence and viewing angles            | `view:incidence_angle`, `view:off_nadir` ([View](https://github.com/stac-extensions/view))      |
| Absolute calibration (gain A, offset B) | `raster:scale` = 1/A, `raster:offset` = B ([Raster](https://github.com/stac-extensions/raster)) |
| Image quality                           | `product:quality_status` ([Product](https://github.com/stac-extensions/product) v1.1.0)         |

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PRs are part of the repository and can be run locally to verify that changes are valid.
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on
your command line run:

```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:

```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:

```bash
npm run format-examples
```
