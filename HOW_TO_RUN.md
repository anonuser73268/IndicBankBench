# Run IndicBankBench

`run` evaluates a candidate model and `compare` compares completed runs. The offline `smoke`
command validates the local harness without model endpoints.

## 1. Install

Clone and install the anonymous code repository:

```bash
git clone https://github.com/anonuser73268/IndicBankBench
cd IndicBankBench
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -e .
```

## 2. Locate the dataset

Check out or download the compatible anonymous dataset snapshot, then point the harness at its
root or directly at its `case_bank/` directory:

```bash
export INDICBANKBENCH_DATA=/path/to/indicbankbench-data
```

The anonymous Hugging Face dataset ID is `anonuser73268/IndicBankBench`:

```bash
hf download anonuser73268/IndicBankBench \
  --repo-type dataset --local-dir ./data
export INDICBANKBENCH_DATA=./data
```

## 3. Run offline checks

With the dataset at `../data`:

```bash
export INDICBANKBENCH_DATA=../data
python -m unittest discover -s indicbankbench/tests
python indicbankbench/scripts/case_lint.py
python -m harness.cli smoke \
  ../data/case_bank/accounts_and_transactions/show_mandates/happy.001.json \
  --stub show_mandates_happy_pass
```

These commands do not contact a candidate or judge endpoint. The smoke command writes ignored
output below `indicbankbench/results/_smoke/`.

## 4. Configure model endpoints

A full run needs two OpenAI-compatible endpoints: a candidate (the system under test) and a
judge (the semantic response grader). Copy `.env.example` to `.env` or export the variables in
your shell:

```bash
CANDIDATE_BASE_URL=http://localhost:8000/v1
CANDIDATE_MODEL=your-model-id
CANDIDATE_API_KEY=EMPTY
JUDGE_BASE_URL=http://localhost:8005/v1
JUDGE_MODEL=your-judge-model-id
JUDGE_API_KEY=EMPTY
```

`EMPTY` is only a placeholder for a local unauthenticated service. Use the service's actual key
for an authenticated endpoint. Do not commit `.env`.

Optional settings:

- `MODEL_MAX_RETRIES`: retry count for candidate and judge requests (default `2`).
- `CANDIDATE_EXTRA_BODY`: JSON object passed to candidate requests when a provider requires it.

Temperature, token limits, and timeouts are configured in
`indicbankbench/config/models.yaml`. Each run records resolved non-secret configuration in
`run.json`.

Before a paid or remote run, verify endpoint availability and cost separately. The candidate
must support tool calling. If it emits hidden reasoning, configure the serving stack so that the
reasoning does not leak into response `content`.

## 5. Evaluate

```bash
python -m harness.cli run --run-id my_model_v1
```

By default, the complete case bank is evaluated three times. Common options are:

```text
--passes N       number of trials per case
--cases PATH     one or more case files/directories
--concurrency N  parallel cases
--fresh          ignore cached scores
--prompt PATH    alternate system prompt
--candidate-label LABEL
```

Run `python -m harness.cli run --help` for the authoritative option list. Runs resume by ID and
reuse scores only when the recorded case and configuration fingerprint matches.

Output is written to `indicbankbench/results/<run-id>/`:

```text
run.json
cases/pass1/<case_id>/transcript.json
cases/pass1/<case_id>/score.json
summary.json
REPORT.md
```

## 6. Compare completed runs

```bash
python -m harness.cli compare run_a run_b --out COMPARISON.md
```

The comparison reports configuration mismatches such as different temperatures, pass counts,
or case-bank hashes. Resolve those differences before interpreting model comparisons.
