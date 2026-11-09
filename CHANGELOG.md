# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [v0.2.0]

### Changed

- `spot:cloud_cover_quotes` accepts 16 letters for SPOT 5 HRS scenes (120 km x 60 km),
  in addition to 8 letters for 60 km x 60 km scenes. The pattern is now `^([A-E]{8}|[A-E]{16})$`
  ([#2](https://github.com/CNES/spot-stac-extension/issues/2)).
- If `spot:sensor_code` is present and is not `S` (HRS), `spot:cloud_cover_quotes` must have 8 letters.

### Added

- Item example of a SPOT 5 HRS scene with 16 cloud cover quotes.

## [v0.1.0]

### Added

- First version, moved from the GEODES data model repository (`schemas/extensions/spot/v0.1.0/`).
- Item fields `spot:sensor_code`, `spot:scene_extent`, `spot:shift_value`, `spot:segment_id`, `spot:scene_rank`,
  `spot:scene_count`, `spot:scene_id`, `spot:coupled_mode`, `spot:coupling_modes` and `spot:cloud_cover_quotes`.
- Link field `spot:coupling_mode` for coupled scenes.
- Collection summaries for the categorical fields.

[Unreleased]: <https://github.com/CNES/spot-stac-extension/compare/v0.2.0...HEAD>

[v0.2.0]: <https://github.com/CNES/spot-stac-extension/compare/v0.1.0...v0.2.0>

[v0.1.0]: <https://github.com/CNES/spot-stac-extension/tree/v0.1.0>
