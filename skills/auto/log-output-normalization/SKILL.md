---
name: log-output-normalization
description: Use when transforming logs into a structured error report with normalized service identifiers and ordering requirements.
---
- Read the output contract and preserve every required field, top-level metadata value, and schema version.
- Normalize service identifiers exactly as specified; when required, lowercase them and replace hyphens with underscores.
- Convert timestamps to the required UTC representation before sorting or emitting records.
- Sort records by the full required compound key, such as service followed by timestamp, rather than by timestamp alone.
- Preserve required counts and ensure they reconcile with the emitted records.
- Validate the final structure by parsing it and checking metadata, normalized identifiers, field presence, and sort order.
=== END===
