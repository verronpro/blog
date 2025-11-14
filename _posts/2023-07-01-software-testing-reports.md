---
layout: article
title: "How-to — Unified Test Reports across Java, Python, and TypeScript"
date: 2023-07-01 09:00:00
categories: [qa,test,report,java,python,typescript]
author: joseph-verron
tags: [testing, docs-as-code, ci]
description: "Practical steps to produce JUnit-style reports in Java, Python, and TypeScript, then aggregate them under the Open Test Reporting initiative."
---

Testing is only useful if you can trust, compare, and publish the results. This guide shows a repeatable way to generate test reports across three common stacks—Java, Python, and TypeScript—and points you to the Open Test Reporting initiative that aims to unify how we view and share results across tools.

## Why reports matter (and what “good” looks like)

- Machine‑readable (XML/JSON) outputs that CI can parse.
- Human‑friendly HTML summaries for quick scan.
- Stable file names and locations so pipelines are simple.
- Consistent schema across languages (JUnit XML is the lowest common denominator).

## Prerequisites

- CI or local shell with the respective toolchains.
- A place to store artifacts (e.g., `./build/reports`, `./reports`, or CI’s artifact step).
- For aggregation: see the Open Test Reporting repo notes below.

## Step 1 — Java (JUnit) produce JUnit XML

Most Java setups already output JUnit XML by default via Maven Surefire/Failsafe or Gradle Test. Keep the defaults, but make paths explicit for CI.

Maven (`pom.xml`):

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-surefire-plugin</artifactId>
      <version>3.2.5</version>
      <configuration>
        <reportsDirectory>${project.build.directory}/test-results</reportsDirectory>
      </configuration>
    </plugin>
  </plugins>
  <pluginManagement/>
</build>
```

Gradle (Kotlin DSL):

```kotlin
tasks.test {
  reports.junitXml.required.set(true)
  reports.junitXml.outputLocation.set(layout.buildDirectory.dir("test-results"))
  reports.html.required.set(true)
}
```

```java
// JUnit Jupiter (5.x) example
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {
  @Test
  void adds_numbers() {
    int result = Calculator.add(2, 3);
    assertEquals(5, result);
  }
}
```

Expected outputs

- Maven: `target/test-results/*.xml`
- Gradle: `build/test-results/test/*.xml` plus `build/reports/tests/test/index.html`

## Step 2 — Python (pytest) emit JUnit XML

Pytest can write JUnit XML with a single flag. Keep a stable path so CI can always pick it up.

```python
# tests/test_calc.py
from mypkg.calc import add

def test_addition():
    assert add(2, 3) == 5
```

CLI:

```bash
pytest -q --junitxml=reports/junit-python.xml
```

Optional HTML (nice locally, heavier in CI):

```bash
pip install pytest-html
pytest --html=reports/pytest-report.html --self-contained-html
```

## Step 3 — TypeScript (Jest) export JUnit XML

Use `jest-junit` to generate JUnit XML. Keep the reporter alongside the default so developers still see the nice CLI output.

```typescript
// src/calc.test.ts
import { add } from './calc';

test('addition', () => {
  expect(add(2, 3)).toBe(5);
});
```

Install and wire the reporter:

```bash
npm i -D jest-junit
```

`jest.config.js`:

```js
module.exports = {
  reporters: [
    'default',
    [ 'jest-junit', { outputDirectory: 'reports', outputName: 'junit-jest.xml' } ]
  ]
};
```

## Step 4 — Aggregate with Open Test Reporting

The Open Test Reporting initiative (ota4j-team) explores a common, tool‑agnostic way to collect and visualize test outcomes. The practical approach today is to standardize on JUnit XML as input and use adapters to feed a unified viewer or storage.

Start here: [ota4j-team/open-test-reporting](https://github.com/ota4j-team/open-test-reporting)

Suggested pattern

1) Produce JUnit XML in each repo as shown above. Keep outputs under a single folder, e.g., `reports/`.
2) In CI, upload `reports/**/*.xml` as a single artifact per build.
3) Point your aggregator/viewer at that artifact (check the adapters in the repo; formats evolve, so prefer their recommended adapter over hand‑rolled scripts).

Verification checklist

- [ ] All three stacks produce at least one `*.xml` under `reports/` or build defaults.
- [ ] CI job exposes the artifact and keeps 30–90 days of history.
- [ ] Flaky tests are identifiable by test case name across languages.

## Troubleshooting

- Java: if XMLs are missing, confirm the Surefire/Failsafe versions and that tests aren’t skipped (`-DskipTests` not set). Gradle: ensure `reports.junitXml.required` is `true`.
- Python: `--junitxml` overwrites the file; use different names per shard if you parallelize.
- Jest: when using `projects` or `--runInBand` vs parallel, make sure `jest-junit` output doesn’t collide; set unique `outputName` via env var if needed.

## Next steps

- Add a lightweight CI stage to always collect and publish reports as artifacts.
- Evaluate the Open Test Reporting adapters/viewer for your stack and wire them on top of the JUnit XML you now produce.
- If you need richer UX, consider layering an HTML viewer for local runs; keep JUnit XML as the contract across repos.
