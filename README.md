# Phenomenological Growth Models Lab

A hands-on walkthrough of phenomenological growth-model fitting for epidemic
incidence data, built on [GrowthFit.jl](https://github.com/rajsubediresearch/GrowthFit.jl).

Six notebooks, each self-contained, working with real outbreak data. No prior
Julia experience assumed — but you should be comfortable with the idea of
fitting a curve to data and reading a confidence interval.

## The notebooks

| # | Notebook | What you'll do |
| - | -------- | -------------- |
| 01 | [Fitting basics](notebooks/01_fitting_basics.ipynb) | Fit a single growth model to an outbreak curve; read the parameters; see what the ODE is actually doing |
| 02 | [Model selection](notebooks/02_model_selection.ipynb) | Fit all seven nested models; compare with AICc; understand why "best fit" and "best model" differ |
| 03 | [Uncertainty](notebooks/03_uncertainty.ipynb) | Parametric bootstrap; MAE, RMSE, 95% PI coverage, and WIS; why a model can win on error and lose on score |
| 04 | [Identifiability](notebooks/04_identifiability.ipynb) | Why some parameters can't be pinned down even when the fit looks perfect |
| 05 | [Forecasting](notebooks/05_forecasting.ipynb) | Short-term forecasts with prediction intervals; evaluating them honestly |
| 06 | [Rolling windows](notebooks/06_rolling_windows.ipynb) | Fit and forecast week by week across the outbreak; which model you would have picked at the time, and why AICc cannot see a turning point |

Work through them in order the first time — 03 assumes 02, and 04 explains
something you'll have noticed in 03.

---

## Getting set up

Everything runs on your own machine. Budget a few minutes for the first
install; after that, starting a notebook takes seconds.

### 1. Install Julia

Use `juliaup`, the official installer.

**Windows** — in PowerShell:

```powershell
winget install julia -s msstore
```

**macOS / Linux**:

```bash
curl -fsSL https://install.julialang.org | sh
```

Restart your terminal, then check it worked:

```bash
julia --version
```

Julia 1.10 or newer.

### 2. Get this repository

With git:

```bash
git clone https://github.com/rajsubediresearch/phenom-growth-lab.git
cd phenom-growth-lab
```

Or download the ZIP from the green **Code** button and extract it.

### 3. Install the packages

From inside the repository folder:

```bash
julia --project=. -e "using Pkg; Pkg.instantiate()"
```

This takes a few minutes the first time — downloading and compiling the ODE
solver — and seconds on every run afterwards.

**Why `--project=.` matters.** This repository ships a `Project.toml` and a
`Manifest.toml` that pin exact versions of every package, including indirect
dependencies. Activating the project means you get precisely the versions these
notebooks were written against, so the numbers you see match the numbers in the
text — regardless of what else is installed on your machine.

If you instead install into your default global environment, Julia resolves
against everything else you already have. That usually works, but newer versions
can resolve differently, and occasionally two packages in the solver stack end
up mismatched and fail to precompile. Using the project environment avoids the
whole category of problem.

The first cell of every notebook activates the environment for you, so if you
forget the flag you are still covered.

### 4. Open the notebooks

Install the Julia Jupyter kernel once:

```bash
julia --project=. -e "using Pkg; Pkg.add(\"IJulia\")"
```

Then launch:

```bash
julia --project=. -e "using IJulia; notebook(dir=pwd())"
```

The first run offers to install Jupyter for you — say yes. A browser tab opens;
go into `notebooks/` and start with `01_fitting_basics.ipynb`.

**Using VS Code instead?** Install the
[Julia extension](https://marketplace.visualstudio.com/items?itemName=julialang.language-julia),
open this folder, and open any `.ipynb` directly. Pick the Julia kernel in the
top right. This is the smoother option if you already use VS Code.

### 5. Threads (recommended)

Notebooks 03, 05 and 06 run their bootstraps in parallel — 06 most heavily. A Jupyter kernel does
**not** inherit `-t` from the command that started Jupyter — the thread count
is baked into the kernel — so this needs a one-off setup:

```bash
julia --project=. -e 'using IJulia; IJulia.installkernel("Julia (4 threads)", env=Dict("JULIA_NUM_THREADS" => "4"))'
```

That adds a second entry to Jupyter's kernel list. Pick it from
**Kernel → Change kernel → Julia (4 threads)**, and the setup cell will report
4 instead of 1.

Use a number no higher than your physical core count; 4 is a safe default.

Results are identical however many threads you use — each bootstrap replicate
is seeded by its index rather than by thread, so the intervals do not depend
on the thread count. This only affects how long you wait.

In VS Code, set `julia.NumThreads` in settings instead.

---

## Troubleshooting

**"Package GrowthFit not found"** — Julia is not using this project. Run the
first cell of the notebook, or start Julia with `--project=.` from the
repository folder.

**A package fails to precompile** — usually a version mismatch from installing
into a global environment rather than this one. From the repository folder:

```bash
julia --project=. -e "using Pkg; Pkg.instantiate(); Pkg.precompile()"
```

If it persists, delete `~/.julia/compiled` (Windows:
`C:\Users\<you>\.julia\compiled`) and run that again.

**"Could not find jalisco_measles.txt"** — the notebook cannot see the `data/`
folder. Launch Jupyter from the repository root rather than from inside
`notebooks/`.

**Plots don't appear** — run the cell again. Julia's plotting stack compiles on
first call, and the first plot in a session can take 30 seconds.

**Everything is slow the first time** — that's Julia compiling. The second run
of the same code is typically 10 to 100 times faster. This catches everyone
coming from R or Python.

## Data

Everything in `data/` is real surveillance data from public sources, with
provenance noted in each notebook. The files are small and committed directly,
so the notebooks work offline once installed.

## Related

- **[GrowthFit.jl](https://github.com/rajsubediresearch/GrowthFit.jl)** — the
  package these notebooks use. API documentation lives there.

## Citation

These notebooks are teaching material. If you use the underlying toolbox in
published work, please cite it:

> Subedi, R. GrowthFit.jl: Phenomenological growth-model fitting, uncertainty
> quantification, and short-term forecasting for epidemic incidence data.
> https://doi.org/10.5281/zenodo.21696876

## License

MIT
