# 🏥 MedTriage

**Open source emergency clinical scoring** — runs entirely in the browser, no installation required.

Combines three internationally validated clinical scores into a unified triage pipeline, designed to be **deterministic, testable and auditable**.

---

## Pipeline

```
Patient parameters
        │
        ▼
┌───────────────┐   ┌───────────────┐   ┌───────────────┐
│     NEWS2     │   │    qSOFA      │   │     HEART     │
│  Deterioration│   │    Sepsis     │   │   Coronary    │
│    0 – 20     │   │    0 –  3     │   │    0 – 10     │
└──────┬────────┘   └──────┬────────┘   └──────┬────────┘
       │                   │                   │
       └───────────────────┴───────────────────┘
                           │
                    Conservative fusion
                    (worst-case rule)
                           │
              ┌────────────▼────────────┐
              │      Global Triage      │
              │  CRITICAL / URGENT /    │
              │  SEMI-URGENT / STABLE   │
              └────────────┬────────────┘
                           │
                    Clinical actions
```

---

## Scores

| Score | Purpose | Range | Reference |
|---|---|---|---|
| **NEWS2** | Patient deterioration | 0–20 | NHS UK, RCP 2017 |
| **qSOFA** | Sepsis screening | 0–3 | JAMA 2016 |
| **HEART** | Acute coronary risk | 0–10 | Netherlands Heart Journal 2010 |

---

## Global triage logic

The urgency level is determined by a **conservative (worst-case) rule** — any single critical signal triggers the highest alert:

| Level | Condition |
|---|---|
| 🔴 **CRITICAL** | NEWS2 ≥ 7 **OR** qSOFA ≥ 2 **OR** HEART ≥ 7 |
| 🟠 **URGENT** | NEWS2 ≥ 5 **OR** qSOFA = 1 **OR** HEART ≥ 4 |
| 🟡 **SEMI-URGENT** | NEWS2 ≥ 1 **OR** HEART ≥ 1 |
| 🟢 **STABLE** | All scores at minimum |

> This strategy prioritises **sensitivity** — never missing a critical case — over specificity.

---

## Architecture

Scoring logic is fully separated from the UI layer:

```js
// Pure scoring engine — deterministic, no side effects
computeFromEngine(input) → { news2, qsofa, heart, triage, auditLog }

// UI layer — display only
render(scores) → DOM updates
```

Each score function is a pure function with no DOM dependency, mirroring the formal properties of the Aether source (`@invariant`, `@example`, sandbox).

---

## Validated results

### Stress test — 10 simultaneous patients

| ID | Profile | NEWS2 | qSOFA | Verdict |
|---|---|---|---|---|
| 01 | Respiratory distress | 12 | 2 | 🔴 CRITICAL |
| 02 | Stable post-op | 0 | 0 | 🟢 STABLE |
| 03 | Confusion + fever | 12 | 3 | 🔴 CRITICAL |
| 05 | Hypothermia + bradycardia | 14 | 2 | 🔴 CRITICAL |
| 06 | Septic shock | 17 | 3 | 🔴 CRITICAL |
| 07 | Stable patient | 0 | 0 | 🟢 STABLE |
| 08 | Moderate fever | 4 | 0 | 🟡 SEMI-URGENT |
| 10 | Sepsis + tachycardia | 15 | 3 | 🔴 CRITICAL |

### Formal invariant verification: **14/14 ✓**

---

## Features

- Real-time multi-score calculation
- Conservative fusion triage
- **Audit log** — full score breakdown per parameter
- **Clinical simulations** — sepsis, ACS, respiratory distress
- Sepsis 1-hour bundle auto-activation
- MACE risk display (HEART)
- Tablet-optimised interface
- 100% offline — single HTML file, no dependencies

---

## Validation limits

MedTriage demonstrates how clinical rules can be made **deterministic, testable and auditable**. The following limits must be clearly stated:

- **Scores implemented per published references, but not clinically validated in this environment.** Algorithms reproduce the original publication formulas (RCP 2017, JAMA 2016, NHJ 2010) but have not been independently validated in this implementation.
- **Note on SpO₂ (NEWS2):** The full NEWS2 standard defines two SpO₂ scales (scale 1 for most patients, scale 2 for patients with hypoxaemic respiratory failure targeting 88–92%). This implementation provides scale 2 via the supplemental O₂ toggle — acceptable for demonstration purposes.
- **Unit tests ≠ clinical validation.** The 14-test suite verifies logical consistency. It does not constitute clinical validation in the regulatory sense (performance studies, sensitivity/specificity on real patient cohorts).
- **IEC 62304 not claimed as achieved.** Compliance with this standard requires, beyond language safety, risk management (ISO 14971), requirements traceability, a documented V&V cycle, and a complete quality process — none of which are covered here.
- **Production use prohibited without regulatory validation.** Any deployment in a real clinical environment would require risk analysis, patient data validation, CE marking as a Software as a Medical Device (SaMD), and a compliant quality process.

> MedTriage illustrates **technical properties useful in a certification process** (determinism, formal invariants, evaluation traceability, side-effect-free sandbox). It does not claim to be a certified medical device.

---

## License

MIT License — © 2026 wilf974
