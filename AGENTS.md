# Project working instructions

Read PROJECT_BRIEF.md and relevant configs before implementing a task.
Preserve user work and follow the user's current scope. Make small, understandable
changes and explain what changed, why, how to run it, and what was verified.
Never invent data, metrics, benchmark results, or successful execution.

## Structure and portability
- Work under the open project root. Do not hard-code D:\ paths in source code.
- Put reusable logic in src/; notebooks explain and explore that logic.
- Install the local package in editable mode; do not append ad hoc paths to sys.path.
- Accept explicit project/data paths. For notebooks use an explicit root or locate
  pyproject.toml upward from the working directory; never depend on notebook __file__.
- Keep raw inputs immutable. Derived files go in interim/, processed/, or artifacts/.
- Store parameters, seeds, selection rules, and experiment settings in configs/.
- Retain one pipeline for small samples and complete datasets.

## Dependencies and execution
- Use the project's .venv Python and python -m pip, never a global pip install.
- Add a library only when needed and explain its role; avoid installing overlapping
  frameworks at once. Verify Python/OS compatibility before adding compiled packages.
- Keep direct dependencies in requirements.txt and verified versions in the environment
  snapshot. Do not invent exact pins. Check imports and pip check after changes.
- Do not run long training jobs or publish remotely unless included in the current task.

## Data quality and evaluation
- Validate keys, types, joins, nulls, and input assumptions at meaningful boundaries.
- Split data before fitting transformations. Prevent leakage according to the problem.
- Record seeds and validation definitions. Compare to simple baselines.
- Add checks for substantive pipeline behavior; avoid tests that merely repeat constants.
- Keep units and aggregation meaning clear. Document missing values separately from zero.
- Keep credentials, raw/private datasets, large checkpoints, and local paths out of Git.
- Keep small curated figures and metrics in reports/ for the portfolio.
