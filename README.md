# NUS Recycling Behaviour Analysis

A group project examining changes in recycling contamination at NUS using difference-in-differences (DiD).

**NUS MSc in Business Analytics · DBA5101 Analytics in Managerial Economics · Group 24 · October 2026**

[Read the report](report.pdf) · [Follow the analysis](recycling_analysis.ipynb) · [Download the dataset](GP2Data.xls)

## What the assignment asks

The professor's brief, *Group Project 2: Analyzing Recycling Behavior at NUS* (AY2026/2027), asks whether two changes in the first NUS recycling trial were associated with meaningful reductions in contamination:

1. Reshaped bin openings in Phase 2.
2. Informational banners in Phase 3, after the openings were removed.

The analysis covers paper, plastic and cans at UTOWN and ENGINE. UTOWN received the interventions. ENGINE kept standard bins throughout and provides the comparison area. The brief asks for a clear report with practical implications, supported by data, code and suitable visuals.

This repository presents the group's work on that question. It covers the first trial in January to March 2020, not the later RecycleRight prototype trial.

## Main finding

The largest observed relative reduction was for **cans during the openings period: about 15 percentage points**. That comparison becomes about 9.5 points when a more recent baseline is used. The baseline gap was already narrowing, so the estimate should not be treated as a proven causal effect.

The other main comparisons do not provide clear statistical evidence of a relative improvement under the reported regression assumptions. A result that is not statistically clear does not establish that the intervention had no effect.

## Files

| File | Purpose |
| --- | --- |
| [report.pdf](report.pdf) | Group report with the question, method, results, interpretation and supporting appendices. |
| [recycling_analysis.ipynb](recycling_analysis.ipynb) | Complete analysis with saved tables, charts and explanations. |
| [GP2Data.xls](GP2Data.xls) | Original course-provided workbook, unchanged. |
| [requirements.txt](requirements.txt) | Pinned Python dependencies for reproducing the notebook. |

Read the report first for the overall argument. The notebook then shows the data checks and calculations in order. Its saved outputs can be read without running Python.

## Trial and data

| Phase | Dates in 2020 | UTOWN | ENGINE |
| --- | --- | --- | --- |
| 1 | 13 January to 10 February | Standard bins | Standard bins |
| 2 | 11 to 22 February | Reshaped openings | Standard bins |
| 3 | 4 to 17 March | Banners; openings removed | Standard bins |

Each recorded row describes an **area on a date**. It does not describe an individual person or bin. Contamination is the percentage of items that do not belong in that recycling stream. Lower contamination is better.

| Variable | Meaning |
| --- | --- |
| `Area` | UTOWN or ENGINE. |
| `Date` | Observation date. |
| `PaperContaminant` | Percentage of incorrectly disposed items in paper bins. |
| `PlasticContaminant` | Percentage of incorrectly disposed items in plastic bins. |
| `CanContaminant` | Percentage of incorrectly disposed items in can bins. |
| `FirstTrialPhase` | Baseline, openings or banners period, coded 1, 2 or 3. |

The workbook contains 85 recorded area-date rows and 170 completely blank rows. There are two missing can readings. Recorded zeros are retained, and missing values are not replaced with zero.

The main analysis matches dates between the two areas separately for each material. Paper and plastic have 18 matched baseline dates, followed by 11 dates in each intervention period. Cans have 17 matched baseline dates and 11 in each later period. Each observed day receives equal weight because item counts are unavailable. These are average daily percentages, not a volume-weighted contamination rate for all collected items.

## Analysis steps

1. **Check the input.** Remove blank rows; check dates, phase labels, duplicate area-date keys, missing values and percentage ranges. Keep the original workbook unchanged.
2. **Match dates.** Compare UTOWN and ENGINE on the same observed dates for each material.
3. **Examine the baseline.** Plot the UTOWN minus ENGINE gap, fit a simple baseline trend and run a comparison within the baseline using 27 January as a placebo date. These checks reveal concerns; they cannot prove parallel trends.
4. **Calculate DiD.** Compare each intervention period with Phase 1 using four averages. Fit the ordinary interaction regression taught in class and verify that its interaction coefficient equals the four-mean calculation.
5. **Inspect uncertainty.** Report confidence intervals and p-values, and examine residual plots. Explain the assumptions behind these calculations.
6. **Repeat with other date choices.** Use all readings, exclude flagged dates, keep weekdays, remove 22 February and use a more recent baseline. Describe the Phase 2 to Phase 3 switch separately.
7. **Interpret the results.** Consider the size of each comparison alongside baseline movement, different uses of the two areas and calendar changes.

The main calculation is:

```text
DiD = (UTOWN after - UTOWN before)
    - (ENGINE after - ENGINE before)
```

The regression is `contamination ~ treated + after + treated:after`. The interaction coefficient is the DiD. Its unit is **percentage points (pp)**, not a percentage change. A negative estimate means contamination fell more, or rose less, at UTOWN relative to ENGINE.

## Main results

| Comparison | Material | DiD (pp) | 95% interval (pp) | p-value |
| --- | --- | ---: | --- | ---: |
| Openings: Phase 2 vs Phase 1 | Paper | -2.23 | [-4.81, 0.34] | 0.088 |
| Openings: Phase 2 vs Phase 1 | Plastic | +0.46 | [-14.40, 15.31] | 0.951 |
| Openings: Phase 2 vs Phase 1 | Cans | -15.02 | [-25.73, -4.32] | 0.007 |
| Banners period: Phase 3 vs Phase 1 | Paper | -1.01 | [-3.54, 1.51] | 0.424 |
| Banners period: Phase 3 vs Phase 1 | Plastic | +9.69 | [-4.68, 24.07] | 0.182 |
| Banners period: Phase 3 vs Phase 1 | Cans | -7.24 | [-16.78, 2.29] | 0.133 |

These are the matched-date estimates from the notebook. The intervals use ordinary OLS assumptions. The can comparison in Phase 2 excludes zero under those assumptions; none of the other five intervals does. This does not resolve the separate concerns about parallel trends or repeated observations.

For example, the Phase 2 can calculation compares the fall at UTOWN with the fall at ENGINE. It does not attribute the entire fall at UTOWN to the openings. Using a recent baseline reduces that DiD from about -15.0 to -9.5 pp, showing why the starting period matters.

## Interpretation and limits

- **Parallel trends is uncertain.** Before treatment, the plastic and can gaps were already narrowing. A non-significant baseline test is not proof that the comparison is valid.
- **There are only two aggregate areas.** More dates do not create more independently treated areas. Repeated observations may also violate the independence and equal-variance assumptions used for the reported intervals.
- **The areas serve different users.** UTOWN includes residential activity, while ENGINE has an academic focus. Calendar and COVID-era changes could affect them differently. The data do not identify a separate numerical COVID effect.
- **Phase 3 follows earlier treatment.** Its estimate may reflect banners, carry-over from openings and other changes over time. Comparing it with Phase 1 does not isolate banners alone.
- **Sensitivity checks do not repair identification.** They show how estimates depend on sample choices. They are not additional experiments or bounds on a true causal effect.
- **Costs and quantities are unavailable.** We cannot calculate savings, total contamination avoided or whether cleaner recycling came with less recycling participation.

A cautious practical reading is to prioritise a better controlled follow-up of can-bin openings, with several comparable sites and records of item counts and operating costs. The present trial does not establish a broad rollout benefit or that banners have exactly zero effect.

## Reading the submitted report

The report is retained as supplied. A few statements should be read with the notebook's more cautious interpretation:

- The 12-day openings period does not establish that habits could not carry into Phase 3. The banner-period comparison cannot isolate banners alone.
- The statement that neither intervention improved plastic means that no clear additional improvement was established. The wide intervals also allow effects in either direction.
- Excluding four high UTOWN paper readings in the baseline is an exploratory sensitivity check. These observations are not confirmed errors. Their later absence does not establish what caused the change. Only the phase-average paper gaps, not every daily gap, stayed below 5 pp.
- The approximately 20-day planning figure is an illustrative calculation assuming independent daily gaps. It does not determine the number of sites or days needed for the proposed trial with repeated observations at several sites. Its variance estimate averages phase variances equally, rather than weighting by their degrees of freedom.

The claim that estimate directions stay the same applies to the date variants in report Table 3. The separate high-paper-reading check changes the Phase 3 paper estimate from negative to positive. These limits do not change the six main arithmetic results above.

## Run the notebook

Use **Python 3.11**. Keep the workbook beside the notebook, as arranged here.

```bash
git clone https://github.com/DarkMatter-404/NUS-Recycling-Behaviour-Analysis.git
cd NUS-Recycling-Behaviour-Analysis
python3.11 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python -m ipykernel install --sys-prefix --name nus-recycling --display-name "NUS recycling (Python 3.11)"
python -m jupyter lab
```

On Windows, activate with `.venv\Scripts\Activate.ps1` in PowerShell. Open `recycling_analysis.ipynb`, select **NUS recycling (Python 3.11)** and use **Restart Kernel and Run All Cells**.

To run without the notebook interface, use the activated environment from the repository root:

```bash
python -m jupyter nbconvert --execute --to notebook --inplace recycling_analysis.ipynb --ExecutePreprocessor.kernel_name=nus-recycling --ExecutePreprocessor.timeout=180
```

The notebook was run from start to finish in the listed Python 3.11 environment. All 22 code cells completed, and the exported result tables agreed with the submitted analysis at the reported precision.

The notebook regenerates tables, figures and numerical exports under `assets/`. That folder is ignored by Git; the notebook retains its displayed outputs. The repository includes the report PDF. Running the notebook does not recompile or update that PDF.

## Team and provenance

This is a **group project**, presented on Aman Raj Mohta's portfolio. The team members are:

- Aman Raj Mohta
- Ananya Vikas Gupta
- Timothy Low
- Yang Yi
- Samuel Chan

The collaborative development repository is [timothy-low/dba5101-analytics-in-managerial-economics](https://github.com/timothy-low/dba5101-analytics-in-managerial-economics). This repository does not claim sole authorship of the team's work.

The report and notebook come from the joint `assignment-2/final-report` files dated 11 October 2026. The PDF is unchanged. The public notebook changes only local file paths, its environment metadata and the description of exports, then reruns the existing analysis. The analytical choices and calculations are retained.

The objective and variable definitions follow the course's Group Project 2 brief and data glossary. The case *Toward Zero Waste: RecycleRight at the National University of Singapore* provides the trial context. Course connections include difference-in-differences and sensitivity checks in Lecture 5, regression and inference in Basic Statistics and Lecture 2, and experimental design in Lecture 4.

The dataset was supplied for coursework, not collected by this team. No open-data licence was supplied with it; inclusion here does not grant additional redistribution or commercial-use rights. No blanket open-source licence is asserted for the course data or jointly authored report. The licensed case PDF and lecture materials are not included.
