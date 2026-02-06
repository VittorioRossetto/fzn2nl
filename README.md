# fzn2nl

Convert a FlatZinc (`.fzn`) model into a readable natural-language summary.

This repo parses a FlatZinc file and prints a structured description of:
- the problem type (satisfaction / minimize / maximize) and objective (best-effort),
- variable counts and rough domain statistics,
- the solver search annotation (when present),
- constraints (with optional categorization and descriptions).

## Requirements

- Python 3.x
- No third-party dependencies (standard library only).

## Quick start

```bash
python3 main.py path/to/model.fzn
```

To group constraints by category (from `fzn_descriptions_categorized.json`):

```bash
python3 main.py path/to/model.fzn --categorize-constraints
```

## Output

The CLI prints three sections:

- **Problem**: category (satisfaction, minimization or maximization), and (if possible) objective domain/degree + a best-effort reconstructed objective expression.
- **Variables**: counts (user vs. compiler-introduced), plus domain-size buckets for integer variables when domains are known.
- **Constraints**: counts by constraint type, arity estimates, and a short description.

## Constraint descriptions

The constraint descriptions in this project are extracted through JSON lookup:

- `fzn_descriptions.json`: mapping of constraint name -> description.
- `fzn_descriptions_categorized.json`: categorized descriptions used by `--categorize-constraints`.

The descriptions and categories are extracted from [MiniZinc Documentation](https://docs.minizinc.dev/en/stable/index.html)
Both files are treated as best-effort resources: if missing or malformed, the program still runs and just omits those descriptions.

## Repo layout

- `main.py`: CLI entrypoint that prints the summary.
- `parser.py`: FlatZinc parser (variables, arrays, constraints, objective, search annotations).
- `variables.py`: variable statistics + natural language rendering.
- `constraints.py`: constraint aggregation + description/categorization support.
- `searchStrat.py`: search annotation rendering.
- `objFunction.py`: objective/problem rendering and best-effort objective reconstruction.
- `nlMappings.py`: natural-language phrase mappings.

## Notes / limitations

- FlatZinc files can be compiled out of MiniZinc and data files, using the [MiniZinc Compiler](https://github.com/MiniZinc/libminizinc).
- Objective reconstruction is best-effort and relies on patterns like `:: defines_var(...)` annotations when present.
- Constraint arity and variable involvement are heuristic when arrays or annotations obscure exact argument structure.

