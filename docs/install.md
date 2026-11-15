# Install Lorevell 2.0

Follow the [source-build instructions](../README.md#install-from-this-checkout) in the repository README. This branch prepares version 2.0.0; it does not publish wheels, install globally, migrate the old personal database or reconfigure an agent automatically.

Create an isolated Python environment, install `maturin`, and run `maturin develop --release` in `lorevell-python`. Put that environment's `bin` directory first on `PATH`. Verify `lorevell --version` reports 2.0.0 and `lorevell --help` lists the vault-first commands.

For literal-only operation:

```bash
lorevell --vault ./knowledge init
lorevell --vault ./knowledge add 'A reusable lesson with evidence.' --text-only
lorevell --vault ./knowledge search 'lesson' --text-only
```

For semantic search, omit `--text-only`; the model may download on first use. Use a disposable vault to verify a new installation, not an existing personal database.

## Lorevell works in a terminal but is unavailable to an agent

Desktop apps and background jobs can launch with a different `PATH` from an interactive shell. An executable installed in `~/.local/bin` or a virtual environment can work in the terminal while the agent reports that Lorevell is unavailable.

1. In the failing environment, run `python -c "import shutil; print(shutil.which('lorevell'))"`. A result of `None` means that the process cannot find the executable.
2. Run the installed executable by absolute path with `--version` in that same environment. If it works, set the caller's executable override to that path, if supported, or add the install directory to the launching process's `PATH`. On Windows, use the path to `lorevell.exe`.
3. Rerun the original operation in the agent or job. For an external source, verify both indexing and a query result with the expected source revision. See [external-source recall](memory-integration.md#read-only-source-snapshots) for a sample snapshot.

If the absolute path fails too, inspect the installation or launcher before investigating the index.

## Set up agent skills

Install project-local agent skills explicitly with `lorevell setup --agent codex --project <project>` or the corresponding `claude` command. Dream uses the existing installed runtime and its authentication. An unavailable runtime is an actionable error; no runtime is downloaded or alternate provider silently chosen. Claude Code is optional for users who select Codex, and vice versa.

For dream, initialize a dedicated Git repository at the vault root (`lorevell init` does this), configure your normal Git identity, resolve unrelated dirty files and inspect `lorevell dream --agent codex --dry-run` before a first run. See [the operational design](v2.md) for snapshots, checkpoints and rollback.
