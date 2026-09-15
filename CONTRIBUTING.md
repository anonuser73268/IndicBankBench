# Contributing to IndicBankBench

IndicBankBench uses separate repositories for the evaluation harness and dataset.

- Submit harness, grader, documentation, and test changes to the code repository.
- Submit case proposals or corrections to the dataset repository after validating them against
  the compatible harness.
- Use the private process in [SECURITY.md](SECURITY.md) for sensitive reports.

Anonymous review repositories:

- Code: https://github.com/anonuser73268/IndicBankBench
- Dataset: https://huggingface.co/datasets/anonuser73268/IndicBankBench

Never contribute real customer data, credentials, personal information, or unsanitised
production transcripts. All fixtures must remain synthetic. A proposed case change must preserve
stable IDs where appropriate and explain any change to expected behavior or grading semantics.

## Development setup

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -e .
export INDICBANKBENCH_DATA=/path/to/indicbankbench-data
```

## Checks

```bash
python -m unittest discover -s indicbankbench/tests
python indicbankbench/scripts/case_lint.py
```

Keep changes focused, add tests for harness behavior, and report all linter errors. Do not
silently change a case, grader rule, expected outcome, or published result for presentation.
