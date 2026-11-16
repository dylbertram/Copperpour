### `move_schematic_objects_between_sheets` moves circuit objects between sheets in `sch_hierarchy` (minor release)

The new `move_schematic_objects_between_sheets` moves UUID-selected symbols, wires, junctions, local and global labels, no-connect flags, text and text boxes from one single-instance sheet of a saved project to another by `offset_x_mm`, `offset_y_mm`. Symbol and pin UUIDs, references, unit membership, properties and footprint associations are kept; instance paths change to the destination sheet.

- A dry run by default. Apply takes the `plan_revision` of a current dry run as `expected_plan_revision`: a missing one is `invalid_argument`, a different one is `stale_target`, and nothing is written.
- Both plans are checked with `kicad-cli` netlist export and ERC on staged copies. Unavailable verification refuses as `plan_blocked`, as do boundary connections, local-label collisions, library conflicts, split multi-unit selections and the other limits in [cross-sheet transfer](../CROSS_SHEET_TRANSFER.md).
- The project's same-named PCB, when present, has its footprint schematic paths and renamed local nets updated in the same transaction. Files the plan read but does not change are held as read-set guards.
- A failure before the transaction journal is written is a `conflict` or `plan_blocked` refusal. After it, the result is `mutation_outcome_uncertain` naming the journal; `copperpour transaction recover` completes the verified move unless a file the plan read has changed since.
