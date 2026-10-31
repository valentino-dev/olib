# olib

A small Python library for analyzing and plotting experimental measurements with uncertainties. It brings together measurement tables, nonlinear curve fitting, uncertainty propagation, and LaTeX table output in a single module.

## What it does

- Stores paired measurements and their uncertainties in a `Table`, with automatic SI-prefix scaling for display.
- Fits user-defined models with SciPy's `curve_fit`, reports parameter uncertainties and reduced chi-squared, and can draw an uncertainty band propagated from the fitted covariance matrix.
- Plots measurements with error bars and exports measurement tables as LaTeX.

## Getting started

Install Python dependencies in your environment:

```bash
python -m pip install numpy pandas matplotlib scipy
```

SciPy must provide `scipy.differentiate.jacobian`. Place `olib.py` in your working directory or on your Python path, then run:

```python
import numpy as np
import matplotlib.pyplot as plt
import olib

x = np.array([1., 2., 3., 4., 5.])
y = np.array([2.1, 3.9, 6.2, 7.8, 10.1])
table = olib.Table(
    x=x, x_err=np.full_like(x, 0.05),
    y=y, y_err=np.full_like(y, 0.2),
    title="Linear measurements", xlabel="Time", ylabel="Distance",
    xunit="s", yunit="m",
)

def line(x, slope, intercept):
    return slope * x + intercept

fig, ax = plt.subplots()
olib.plotData(ax, table, model=line, p0=[2, 0], label="Measurements")
plt.show()
```

`fit_function` can also be called directly and returns fitted parameters and their covariance matrix; when plotting the fitted curve, it additionally returns the propagated uncertainty values. Fit weights use `y_err` when all its values are nonzero. Reduced chi-squared divides by the supplied `y_err` values, so use positive, nonzero uncertainties and more observations than fitted parameters.

To write a LaTeX table, call `table.saveAsPDF(path="./", filename="measurements.tex")`. Despite the method name, it writes a `.tex` file, not a PDF. The output uses `\num` from the LaTeX `siunitx` package; include that package when compiling the table.

## Project status

This is a compact, exploratory utility rather than a packaged release. Some helpers are incomplete or need repair: the `model` class is a placeholder, `CSV_to_PDF` relies on a commented-out import, and `setSpace` refers to attributes that `Table` does not define. Review results and uncertainty assumptions before using them in formal analysis.
