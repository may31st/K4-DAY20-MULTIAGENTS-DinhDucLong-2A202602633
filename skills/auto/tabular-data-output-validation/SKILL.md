---
name: tabular-data-output-validation
description: Use when cleaning tabular records and producing structured summaries or normalized output files.
---
- Read the output contract first; record required files, columns, column order, types, metadata, and normalization rules.
- Count source data rows before deduplication, including duplicate records.
- Apply the specified deduplication key and eligibility rules before calculating distinct-record counts or aggregates.
- Parse monetary values with decimal-safe arithmetic and encode required minor units as integers.
- Normalize timestamps to the required timezone and exact output format; normalize categories to their canonical spellings.
- Generate every required output artifact, including both machine-readable summaries and cleaned tables.
- Reopen and validate each artifact against its schema, row counts, types, ordering, and required metadata.
- Reconcile summary counts and totals against the cleaned records before finishing.
