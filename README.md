# IndicBankBench

IndicBankBench evaluates the safety and reliability of tool-using language-model assistants in
Indian retail banking. It contains an evaluation harness, deterministic safety and action
checks, a mocked banking environment, an LLM-based response grader, and offline smoke-test
support. The 799-case dataset is distributed separately.

Anonymous-review locations:

- Code: https://github.com/anonuser73268/IndicBankBench
- Dataset: https://huggingface.co/datasets/anonuser73268/IndicBankBench

## Requirements

- Python 3.9 or newer
- The separately released IndicBankBench dataset for data-dependent tests or evaluations
- Two OpenAI-compatible endpoints only for a full evaluation: one candidate and one judge

The unit tests and documented smoke test do not use a network service or credentials.

## Local installation

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -e .
```

## Offline verification

From this repository, with the dataset repository checked out at `../data`:

```bash
export INDICBANKBENCH_DATA=../data
python -m unittest discover -s indicbankbench/tests
python indicbankbench/scripts/case_lint.py
python -m harness.cli smoke \
  ../data/case_bank/accounts_and_transactions/show_mandates/happy.001.json \
  --stub show_mandates_happy_pass
```

The linter can report documented warnings and informational findings as well as errors. An
offline release check passes only when it scans all 799 cases and reports zero errors.

## Full evaluation

Copy `.env.example` to `.env`, configure the candidate and judge endpoints, and set the dataset
location:

```bash
cp .env.example .env
export INDICBANKBENCH_DATA=/path/to/indicbankbench-data
python -m harness.cli run --run-id my_model_v1
```

Full evaluation calls external model endpoints and may incur cost. It is not required for the
offline release smoke test. See [HOW_TO_RUN.md](HOW_TO_RUN.md) for configuration and CLI details.

## What is graded

Each case is evaluated in four phases:

```text
S — Safety -> A — Actions -> R — Response -> PASS / FAIL
                                      |
                                      `-> Q — Quality (reported separately)
```

A case passes only when every active safety (S), action (A), and response (R) gate passes.
Quality (Q) is advisory. Safety and tool correctness are deterministic except for the explicitly
documented confirmation resolver; the response gate is judged semantically.

- [Architecture and grading model](docs/ARCHITECTURE.md)
- [Metric definitions](docs/METRICS.md)
- [Failure-code reference](docs/CODES.md)
- [Complete run guide](HOW_TO_RUN.md)

## Data

The case bank is intentionally not tracked in this code repository. Set
`INDICBANKBENCH_DATA` to either the dataset-repository root or its `case_bank/` directory, or pass
`--cases PATH` to an evaluation command. The compatible anonymous dataset snapshot contains 799
JSON cases under `case_bank/`, is available as `anonuser73268/IndicBankBench`, and is licensed
separately under CC BY 4.0.

## Contributing and security

See [CONTRIBUTING.md](CONTRIBUTING.md) for development and validation instructions. Report
security issues privately as described in [SECURITY.md](SECURITY.md). Do not place real customer
data, credentials, or unsanitised production transcripts in an issue or contribution.

## Citation during anonymous review

Use the anonymous paper citation supplied with the submission. Do not attempt to infer or name
the authors from the artifact. A temporary artifact-only BibTeX entry is available in
[CITATION.bib](CITATION.bib); it should be replaced with the final paper citation after review.

## License and disclaimer

The software is licensed under the [MIT License](LICENSE). The dataset has a separate CC BY 4.0
license. IndicBankBench is synthetic and research-only; it is not intended for live banking,
regulatory, legal, or financial decisions. See [DISCLAIMER.md](DISCLAIMER.md).
