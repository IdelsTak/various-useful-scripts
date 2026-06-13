# cloc: Practical Usage Guide for Java Codebases (Correct Model)

## 1. What cloc actually is

`cloc` is a filesystem-based static counting tool.

It answers one question:

> How many lines exist in these files, grouped by language?

It does NOT understand:

* test vs production meaning
* architecture layers
* domain boundaries
* module semantics

Any “test vs production” interpretation is **not inherent to cloc**. It is only a consequence of how you structure directories.

---

## 2. Core limitation (important)

`cloc` has no concept of:

* “main code”
* “test code”
* “feature code”
* “domain code”

It only sees:

* file paths
* file extensions
* directory grouping provided by the user

So any separation like:

```text
src/main/java
src/test/java
```

is purely conventional, not semantic intelligence.

---

## 3. What cloc is actually good for

Use `cloc` for:

* total size of a codebase
* language distribution (Java vs XML vs HTML etc.)
* growth tracking over time
* detecting build/generated noise (with exclusions)
* rough structural decomposition by directory

Do NOT use it for:

* architectural evaluation
* code quality assessment
* test coverage reasoning
* design correctness

---

## 4. Correct mental model

`cloc` operates like this:

```text
INPUT: files on disk
PROCESS: group by extension + directory
OUTPUT: aggregated counts (blank, comment, code)
```

There is no semantic interpretation step.

---

## 5. Core usage patterns (correct interpretation)

### 5.1 Raw repository scan (requires exclusions)

```bash id="p9xkq1"
cloc . --exclude-dir=.git,target,.idea,.mvn,build,dist,out,node_modules
```

This gives:

* total lines per language
* file counts
* noise-reduced dataset

Use this only for **global repository overview**.

---

### 5.2 Source-only analysis (recommended baseline)

```bash id="m2kq8r"
cloc src
```

This measures:

* all Java code under `src`
* all supporting resources in `src`

It does NOT distinguish intent.

---

### 5.3 Directory comparison (structural comparison, not semantic)

```bash id="k4zq2w"
cloc src/main/java
cloc src/test/java
```

This produces two independent reports.

Important correction:

* This does NOT produce a “production vs test split”
* It only produces **two separate directory-based counts**

Any comparison between them is done manually by the user.

---

### 5.4 File-level inspection (most important for architecture work)

```bash id="n7wq3t"
cloc src/main/java --by-file
cloc src/test/java --by-file
```

This is the most useful mode because it reveals:

* LOC per class
* distribution of class sizes
* uneven complexity hotspots
* test size distribution

This is where `cloc` becomes structurally meaningful.

---

## 6. What is actually meaningful in cloc output

### 6.1 Language distribution

Example signals:

* Java dominant → backend system
* XML heavy → UI or config-heavy system
* HTML/JS present → mixed frontend/backend leakage

This helps detect **ecosystem composition**, not architecture.

---

### 6.2 LOC per file

```text id="x8qv1a"
LOC / number of files
```

This is the strongest structural signal `cloc` provides.

Interpretation:

* > 200 LOC/file → large classes, low decomposition
* 50–150 LOC/file → typical Java design
* < 40 LOC/file → highly decomposed architecture

---

### 6.3 Blank vs code ratio

High blank ratios usually indicate:

* formatting-heavy style
* highly structured formatting rules
* or generated code formatting

This is stylistic, not architectural.

---

## 7. What cloc cannot do (critical boundaries)

`cloc` cannot:

* identify tests vs production code logically
* map test files to production classes
* identify modules or bounded contexts
* measure complexity
* measure coupling
* measure correctness
* detect duplication meaningfully

It is blind to semantics.

---

## 8. Misconceptions to avoid

### 8.1 “cloc can compare test vs production”

Incorrect.

What actually happens:

* you manually separate directories
* cloc counts them independently
* you interpret the difference

cloc does not classify anything.

---

### 8.2 “cloc shows architecture”

Incorrect.

It shows filesystem structure only.

Architecture requires:

* dependency analysis tools
* code graph tools
* domain modeling review

---

### 8.3 “LOC is a quality metric”

Incorrect.

LOC measures size, not:

* correctness
* design quality
* maintainability
* performance

---

## 9. Correct workflow for Java projects

### Step 1: understand total size

```bash id="z2wq9m"
cloc src
```

---

### Step 2: inspect structure by directory

```bash id="v4kq2p"
cloc src/main/java
cloc src/test/java
```

---

### Step 3: inspect class-level distribution

```bash id="c7mq2t"
cloc src/main/java --by-file
cloc src/test/java --by-file
```

---

### Step 4: remove noise in full repo analysis

```bash id="a9pq1r"
cloc . --exclude-dir=.git,target,.idea,.mvn,build,dist,out,node_modules
```

---

## 10. How to interpret results correctly

When you see:

```text id="r2kq9x"
src/main/java → X LOC
src/test/java → Y LOC
```

This means only:

> "There are X lines of Java files located in main, and Y lines in test."

Nothing more.

Any ratio or interpretation beyond that is done externally.

---

## 11. Key principle

`cloc` is a measurement tool, not an analysis tool.

Its correct role is:

> “Provide raw structural counts that you interpret outside the tool.”

It should never be used as:

* architecture validator
* quality indicator
* test coverage proxy
* design evaluator
