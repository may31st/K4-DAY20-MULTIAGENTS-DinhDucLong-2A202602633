---
name: code-maintenance-completion
description: Use when fixing bugs in an existing code package with repository rules for tests, annotations, or changelog entries.
---
- Read the repository instructions and inspect the affected code before editing.
- Treat existing tests as read-only; add or update tests only in permitted new test files.
- Add a regression test for each distinct bug fixed, and run the required test suite.
- Add parameter and return annotations to every public function in the package.
- Record every fix in the changelog under the required unreleased heading and use the prescribed bullet format.
- Review the diff to confirm protected files are unchanged and all required artifacts are present.
