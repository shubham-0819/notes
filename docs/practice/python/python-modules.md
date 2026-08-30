# Exercises for Data Engineering

Each section covers one stdlib module, why it matters for pipeline/ETL code, and 3 exercises
(easy → hard) framed around realistic scenarios. Do them in order within a section; the last
exercise usually forces you to combine several features of that module.

No solutions are included on purpose — write the code, run it, and come back with questions
about specific approaches if you get stuck.

---

## 1. `collections` — the module you'll use daily

Why: `Counter`, `defaultdict`, `namedtuple`, and `deque` replace a huge amount of manual
bookkeeping code you'd otherwise write with plain dicts/lists.

**1.1 (Easy)** You have a list of dicts representing rows from a Delta table:
`rows = [{"user": "a", "event": "click"}, {"user": "b", "event": "view"}, ...]`.
Use `Counter` to compute event counts per type, and separately the top-3 most common events.

**1.2 (Medium)** Use `defaultdict(list)` to group the same rows by `user`, producing
`{"a": [event1, event2, ...], ...}` without checking `if key not in dict`.

**1.3 (Hard)** Implement a sliding-window "last N events per user" tracker using
`defaultdict(lambda: deque(maxlen=N))`, fed row-by-row (simulating a streaming micro-batch).
Then use `namedtuple` (or `typing.NamedTuple`) to give each row a typed shape instead of a raw dict,
and adjust your solution to use it.

---

## 2. `itertools` — for expressing loops declaratively

Why: cleaner and often faster than nested loops; essential for windowing, batching, and
combinatorial config generation (e.g., generating parameter grids for job configs).

**2.1 (Easy)** Given a flat list of file paths from multiple partitions, use `itertools.chain`
to flatten a list-of-lists of paths into one list without a nested loop.

**2.2 (Medium)** Use `itertools.groupby` to group a **sorted** list of records by `date` key and
compute a per-group aggregate (e.g., sum of a `revenue` field). Explain in a comment why
`groupby` requires the input to be pre-sorted by the same key, and what happens if it isn't.

**2.3 (Hard)** Write a `chunked(iterable, size)` generator using `itertools.islice` that batches
an arbitrarily large iterable into lists of `size` (useful for batch-writing to a sink in chunks
rather than loading everything into memory). Then use `itertools.product` to generate all
combinations of `{"env": ["dev","prod"], "region": ["us","eu"]}` as job-config dicts.

---

## 3. `functools` — decorators, caching, reducing

Why: `lru_cache` for cheap memoization (e.g., caching a schema lookup or a small reference table
in a UDF), `partial` for currying config into functions, `wraps` for writing correct decorators,
`reduce` for fold-style aggregation.

**3.1 (Easy)** Use `functools.lru_cache` to memoize a function that "looks up" a currency
conversion rate (simulate with a slow `time.sleep(1)` call). Show the second call is instant.

**3.2 (Medium)** Write a `@retry(times=3, exceptions=(ValueError,))` decorator using
`functools.wraps` that retries a flaky function (e.g., simulating a transient API/DB read
failure) and re-raises after exhausting attempts.

**3.3 (Hard)** Use `functools.reduce` to implement a mini "pipeline runner": given a list of
transform functions `[f1, f2, f3]` each taking and returning a DataFrame-like object (use a dict
or list as a stand-in), reduce them into a single function that applies them in sequence. Then
rewrite it using `functools.partial` to pre-bind extra arguments (e.g., a `config` dict) to each
transform before reducing.

---

## 4. `contextlib` — resource management beyond `with open(...)`

Why: cleanup for DB connections, temp files, timers around job stages, suppressing expected
errors cleanly.

**4.1 (Easy)** Write a context manager using `@contextlib.contextmanager` that times a block of
code and logs "Stage X took N seconds" on exit — use it to wrap a fake "extract" and "load" stage.

**4.2 (Medium)** Use `contextlib.suppress` to replace a `try/except/pass` for a case where a
temp file might not exist during cleanup.

**4.3 (Hard)** Use `contextlib.ExitStack` to open a *variable* number of file/resource handles
(e.g., one output file per partition key, unknown count until runtime) and guarantee they all
close even if one write fails partway through.

---

## 5. `dataclasses` — typed structures without boilerplate

Why: cleaner than raw dicts for representing config objects, table schemas, or job parameters;
plays nicely with `typing`.

**5.1 (Easy)** Define a `@dataclass JobConfig` with fields `source_path: str`, `target_table: str`,
`batch_size: int = 1000`. Instantiate it two ways: positional and keyword.

**5.2 (Medium)** Add `frozen=True` to make it immutable, then use `dataclasses.replace()` to
produce a modified copy (e.g., a different `batch_size`) without mutating the original — useful
for representing config variants per environment.

**5.3 (Hard)** Add a `__post_init__` that validates `batch_size > 0` and raises `ValueError`
otherwise. Then use `dataclasses.asdict()` to serialize the config to a dict you could dump as
JSON for logging job parameters.

---

## 6. `typing` — self-documenting, IDE/mypy-friendly code

Why: on a team, typed signatures on ETL functions catch mistakes before runtime and make
notebooks easier to navigate.

**6.1 (Easy)** Add full type hints (including return type) to a function
`def load_rows(path): ...` that returns a `list[dict[str, Any]]`.

**6.2 (Medium)** Use `typing.TypedDict` to define the exact shape of a row (e.g.,
`class UserRow(TypedDict): user_id: str; event: str; ts: float`) and annotate a function that
consumes `list[UserRow]`.

**6.3 (Hard)** Use `typing.Protocol` to define a structural type `SupportsWrite` with a `write(self, rows) -> None`
method, and write a function `def flush(sink: SupportsWrite, rows) -> None` that works with *any*
object implementing that method — no inheritance required. Demonstrate with two unrelated classes
that both satisfy the protocol.

---

## 7. `pathlib` — stop using `os.path.join`

Why: cleaner, cross-platform path handling for reading/writing local or mounted (`/dbfs/...`)
paths.

**7.1 (Easy)** Use `pathlib.Path` to list all `.csv` files under a directory (`Path.glob("*.csv")`)
and print their sizes via `.stat().st_size`.

**7.2 (Medium)** Use `Path.rglob` to recursively find all files matching `*.parquet` under a
partitioned directory structure (e.g. `data/year=2024/month=01/file.parquet`), and extract the
`year=`/`month=` partition values from each path's parts.

**7.3 (Hard)** Write a function that takes a base `Path` and a `dict` of partition key/values and
constructs the correctly nested output path (e.g. `Path("data") / "year=2024" / "month=01"`),
creating intermediate directories with `mkdir(parents=True, exist_ok=True)` only if they don't exist.

---

## 8. `re` — regex for log/text parsing

Why: parsing driver/executor logs, validating column name patterns, cleaning messy string columns.

**8.1 (Easy)** Write a regex to extract timestamp, log level, and message from a line like
`2026-08-01 10:22:31 ERROR Failed to read partition`. Use named groups (`(?P<ts>...)`).

**8.2 (Medium)** Use `re.sub` to clean a column of messy strings (e.g., strip non-alphanumeric
characters from IDs, collapse multiple spaces to one).

**8.3 (Hard)** Write a function using `re.finditer` that extracts *all* key=value pairs from a
Spark config-style string like `"spark.sql.shuffle.partitions=200 spark.databricks.delta.optimizeWrite=true"`
into a dict, handling values that may or may not be quoted.

---

## 9. `datetime` (+ `zoneinfo`) — timestamps will bite you eventually

Why: timezone bugs are one of the most common silent data-quality issues in pipelines.

**9.1 (Easy)** Parse an ISO string into a `datetime` object with `datetime.fromisoformat`, and
format it back out in `"%Y-%m-%d %H:%M"` format.

**9.2 (Medium)** Given a naive `datetime` assumed to be UTC, use `zoneinfo.ZoneInfo` to convert
it to `"America/New_York"` and to `"Asia/Kolkata"`, printing both, and explain what would go wrong
if you skipped setting the source zone explicitly.

**9.3 (Hard)** Write a function `bucket_by_day(events: list[dict])` that takes events with a
`ts: datetime` (mixed timezones) and buckets them into UTC calendar days, correctly normalizing
timezone before bucketing — this mirrors a very common "off-by-one-day" bug in daily aggregation jobs.

---

## 10. `json` — config and semi-structured data

Why: reading job configs, writing structured logs, handling nested API payloads before flattening.

**10.1 (Easy)** Load a nested JSON config from a string with `json.loads`, then dump it back with
`indent=2` and `sort_keys=True`.

**10.2 (Medium)** Write a custom `json.JSONEncoder` subclass that knows how to serialize
`datetime` and `Decimal` objects (both common in row data) so `json.dumps(row, cls=YourEncoder)`
doesn't blow up.

**10.3 (Hard)** Write a recursive "flatten" function that turns a nested JSON object like
`{"user": {"id": 1, "address": {"city": "Delhi"}}}` into a flat dict
`{"user.id": 1, "user.address.city": "Delhi"}` — a very common preprocessing step before writing
semi-structured JSON into a flat Delta table schema.

---

## 11. `logging` — replace your `print()` statements

Why: real jobs need structured, level-based logs that show up correctly in Databricks driver logs.

**11.1 (Easy)** Configure a logger with `logging.basicConfig`, log at `INFO`, `WARNING`, and
`ERROR` levels, and show the difference when you raise the level to `WARNING`.

**11.2 (Medium)** Create a custom `Formatter` that includes job name and stage as part of every
log line (e.g., `[job=ingest_users][stage=extract] INFO: ...`), using a `LoggerAdapter` or
`extra=` dict.

**11.3 (Hard)** Wrap a multi-stage pipeline function so each stage's start, success/failure, and
duration are logged automatically via a decorator (combine with your `contextlib` timer from
Exercise 4.1), and failures log the full stack trace with `logger.exception`.

---

## 12. `concurrent.futures` — I/O-bound parallelism (careful: not for Spark work itself)

Why: useful for things *outside* Spark's own parallelism — e.g., calling several REST APIs, or
reading many small files from cloud storage concurrently in driver-side code.

**12.1 (Easy)** Use `ThreadPoolExecutor.map` to fetch (simulate with `time.sleep`) 10 "API calls"
concurrently instead of sequentially, and compare wall-clock time.

**12.2 (Medium)** Use `as_completed` instead of `map` so you can process results as they arrive
and log/handle exceptions from individual futures without one failure killing the whole batch.

**12.3 (Hard)** Build a small "fan-out fan-in" utility: given a list of file paths, read each
concurrently with a thread pool (I/O-bound), then aggregate results in the main thread — explain
in comments why you'd use `ThreadPoolExecutor` here rather than `ProcessPoolExecutor`.

---

## 13. `enum` — safer than magic strings

Why: `if stage == "extract"` scattered through code is fragile; enums make illegal states
harder to represent.

**13.1 (Easy)** Define `class Stage(enum.Enum): EXTRACT = "extract"; TRANSFORM = "transform"; LOAD = "load"`
and refactor a stage-dispatch function to use it instead of raw strings.

**13.2 (Medium)** Use `enum.auto()` and iterate over all members with a loop to build a
dispatch dict `{stage: handler_function}`.

**13.3 (Hard)** Use `enum.Flag` to represent combinable pipeline options (e.g.,
`VALIDATE | DEDUPE | LOG`) that can be OR'd together and checked with bitwise `in`.

---

## 14. `abc` — enforce a contract across pipeline steps

Why: if you're writing multiple "extractor" or "loader" classes for different sources/sinks, an
abstract base class prevents someone from forgetting to implement a required method.

**14.1 (Easy)** Define `class BaseExtractor(abc.ABC)` with an abstract method `extract(self) -> list[dict]`.
Try instantiating it directly and observe the error; then implement a concrete subclass.

**14.2 (Medium)** Add a second abstract method `validate(self, rows) -> bool` and a *concrete*
(non-abstract) method `run(self)` on the base class that calls `extract()` then `validate()` —
demonstrating the Template Method pattern.

**14.3 (Hard)** Write two concrete extractors (`CsvExtractor`, `ApiExtractor`) and a function
`run_all(extractors: list[BaseExtractor])` that runs them polymorphically, collecting failures
per-extractor rather than stopping at the first error.

---
