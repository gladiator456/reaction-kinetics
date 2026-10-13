# ⚗️ Reaction Kinetics Analyzer

![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?logo=streamlit&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-8CAAE6?logo=scipy&logoColor=white)

A Streamlit web app that takes concentration-versus-time data from a chemical reaction and works out the **reaction order**, the **rate constant `k`** and the **half-life**. It compares the integer orders 0 to 3 and also searches for the best-fitting non-integer order.

## What it does

Enter your data as `Time` and `Concentration` (an editable table, pasted CSV, or an uploaded CSV/Excel file), and the app:

- **Cleans the input.** Common column names such as `t`, `C` and `[A]` are recognised. Rows that are invalid, duplicated, have negative time, or have non-positive concentration are removed with a warning, and at least 3 valid points are required.
- **Fits zero, first, second and third order** by linear regression on the matching transformed data, and ranks them by R².
- **Searches for a continuous order `n`** in a range you choose (default 0 to 5) by maximising R² with SciPy's bounded scalar optimiser, and tests `n = 1` explicitly because that case is singular in the general formula.
- **Reports** the rate constant with its units, the half-life, and the differential and integrated rate laws rendered in LaTeX.
- **Plots** concentration against time, the linearised plot for each order, and the best continuous-order fit.
- **Exports** the analysis results and the transformed data as CSV.

## Method

Each integer order is linear in a different transformation of concentration:

| Order | Rate law | Plot against `t` | Slope gives | Half-life |
| :---: | --- | --- | --- | --- |
| 0 | `rate = k` | `[A]` | `-k` | `[A]₀ / 2k` |
| 1 | `rate = k[A]` | `ln[A]` | `-k` | `ln 2 / k` |
| 2 | `rate = k[A]²` | `1/[A]` | `k` | `1 / (k[A]₀)` |
| 3 | `rate = k[A]³` | `1/[A]²` | `2k` | `3 / (2k[A]₀²)` |

For a general order `n ≠ 1`, the integrated law is `1/[A]^(n-1) − 1/[A]₀^(n-1) = (n−1)·k·t`. The app plots `1/[A]^(n−1)` against `t` for candidate values of `n`, keeps the `n` with the highest R², and takes `k` from the slope divided by `(n−1)`. For the integer orders, the app also flags whether the fitted `k` is positive, and it describes an R² of 0.99 or more as an excellent linear fit.

## Run it

```bash
git clone https://github.com/gladiator456/reaction-kinetics.git
cd reaction-kinetics
pip install -r requirements.txt
streamlit run reaction_order_app.py
```

Example input:

```csv
Time,Concentration
0,1.000
10,0.741
20,0.549
30,0.407
40,0.301
```

(This sample follows first-order decay, with `k` close to 0.03 per time unit.)

## Limitations

- The order is chosen by comparing R² on **linearised** data. Nonlinear least squares on the raw concentrations would weight the points more fairly.
- No confidence interval is reported for `n` or `k`.
- It analyses a single reactant in an isothermal batch experiment, with one reaction order for the whole run.

## What this project demonstrates

- Applying chemical-kinetics theory (integrated rate laws, half-lives, units of `k`) in code.
- Numerical fitting with SciPy (`linregress`, `minimize_scalar`), including handling the singular point at `n = 1`.
- Building and validating an interactive data tool: input cleaning, error messages, plots and downloadable results.

## Tech stack

Python, Streamlit, Pandas, NumPy, SciPy, Matplotlib.

## Author

Anuj Saxena, B.Tech Chemical Engineering, IIT Jammu.
