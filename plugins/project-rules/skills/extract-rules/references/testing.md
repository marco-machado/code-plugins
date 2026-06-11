# Extracting Testing Conventions

## What to examine

Locate the tests (glob for `*.test.*`, `*.spec.*`, `test_*.py`, `*_test.go`, `tests/`, `__tests__/`) and sample 10 to 15 test files of different kinds, plus the test runner config:

- **Framework and runner**: jest, vitest, pytest, go test, rspec, etc.; the config file and notable settings (setup files, environment, coverage thresholds that are actually configured).
- **Placement and naming**: colocated next to source vs a parallel `tests/` tree; the file suffix actually used; how integration and e2e tests are separated from unit tests (directory, suffix, tag, build flag).
- **Test structure**: `describe`/`it` nesting depth and what the describe blocks name (the unit? the method? the scenario); arrange-act-assert vs given-when-then; one assertion per test or several; use of `beforeEach` vs inline setup.
- **Test naming**: the sentence pattern of test names ("returns X when Y", "should ...", `test_<method>_<condition>`); consistency matters more than the specific pattern.
- **Doubles and mocking**: mocking library vs hand-written fakes vs real implementations; whether module mocking (`jest.mock`, `monkeypatch`) is embraced or avoided; network stubbing approach (MSW, VCR, recorded fixtures); what is never mocked (e.g. the database in integration tests).
- **Fixtures and factories**: factory functions, fixture files, builders, seeded databases; where shared test helpers live.
- **Assertion style**: expect/assert flavor, custom matchers, snapshot usage and whether snapshots are small and reviewed or sprawling.
- **Parametrization**: table-driven tests in Go, `pytest.mark.parametrize`, `it.each` — used routinely or rarely.

## Convention vs accident

- The oldest test files often predate the current conventions; weight recent tests more heavily.
- A handful of snapshot tests in a codebase of explicit assertions is not a snapshot convention.
- Different test kinds legitimately have different rules; scope findings as unit vs integration vs e2e rather than averaging.

## What to report

Rules such as: where a new test file goes and what it is named, the structure and naming pattern for test cases, the sanctioned mocking approach and its limits, how to create test data, and which kind of test to write for which kind of change. Include the command to run the tests if discoverable from package scripts, Makefile, or CI config.
