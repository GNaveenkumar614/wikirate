# README – WikiRate ESG Dataset for ML Evaluation

## Overview
This repository contains a Python notebook and JSON export that transform ESG metrics from [WikiRate](https://wikirate.org/) into a structured dataset suitable for ML models. The goal was not to maximize volume, but to build a small, high-quality dataset conforming to the target schema.

## How WikiRate data was accessed
- Used the official `wikirate4py` Python SDK to connect to WikiRate’s REST API with an API key.
- For each chosen metric and year, used a `wikirate4py.Cursor` over `api.get_answers(...)` (with pagination) to fetch up to a fixed maximum number of answers per metric-year.

## Metrics and questions chosen
Three metrics are Global Reporting Initiative (GRI) corporate-level metrics and map cleanly to numeric ESG quantities, taking one from each category.

### 1. Environmental (E) – Scope 1 emissions
- **Designer:** Global Reporting Initiative
- **Metric:** “Direct greenhouse gas (GHG) emissions (Scope 1), GRI 305-1-a (formerly G4 EN15-a)”
- **Question used:** “What is the total amount of Scope 1 direct greenhouse gas emissions in metric tonnes of CO2 equivalent?”
- **Custom ID in code:** `GRI3051aScope1GHG`
- **Unit in structured data:** `tCO2e`

### 2. Social (S) – Total employees
- **Designer:** Global Reporting Initiative
- **Metric:** “Employees, GRI 102-8-a (formerly G4 10-a)”
- **Question:** “What is the company’s total number of employees?”
- **Custom ID:** `GRI1028aTotalEmployees`
- **Unit:** `employees`

### 3. Governance/Social – Female employees
- **Designer:** Global Reporting Initiative
- **Metric:** “Female employees, GRI 102-8-a (formerly G4 10-a)”
- **Question:** “What is the total number of female employees?”
- **Custom ID:** `GRI1028aFemaleEmployees`
- **Unit:** `employees`

## Data quality issues observed
The following recurring data issues were observed:

- Missing or non-informative values
- Duplicate company-year records
- Incomplete source metadata
- Heterogeneous value formats

## Assumptions and trade-offs
To keep the dataset generic and usable, a few assumptions and trade-offs were made:

- Only answers with at least one usable source document are considered valid; entries with missing URLs were filtered out.
- For duplicate company-year entries, the first valid answer returned by the API was used to keep the data simple and deterministic while still providing variety across companies.
- Units are fixed per metric (e.g., `tCO2e` for Scope 1, `employees` for headcount).
- The question and `custom_id` in the final JSON are taken directly from the chosen metric definitions rather than rephrased, to avoid introducing ambiguity or drift between WikiRate and the evaluation dataset.

## What could be improved with more time
With more time, the following extensions could be considered:

- Richer parsing of comments for page numbers and methodological details
- More systematic unit and scale handling for large numbers of records
- Broader metric and year coverage to increase dataset diversity
- Analysis and extraction of useful information from the comments section
