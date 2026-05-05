# 🏥 MedTriage

**Système de scoring médical d'urgence** implémenté en Aether v1.3.

Combine trois scores cliniques validés en un pipeline de triage unifié, conçu pour être **vérifiable, auditable et certifiable** grâce aux garanties formelles du langage Aether (`@invariant`, `@example`, sandbox).

---

## Scores implémentés

| Score | Usage | Bornes | Référence |
|---|---|---|---|
| **NEWS2** | Détérioration patient | 0–20 | NHS UK, 2017 |
| **qSOFA** | Dépistage sepsis | 0–3 | JAMA 2016 |
| **HEART** | Risque coronarien aigu | 0–10 | Netherlands Heart Journal 2010 |

---

## Structure

```
MedTriage/
├── src/
│   ├── news2.aether            # National Early Warning Score 2
│   ├── qsofa.aether            # quick Sepsis-related Organ Failure Assessment
│   ├── heart.aether            # HEART Score (SCA)
│   └── triage_pipeline.aether  # Pipeline d'orchestration unifié
├── tests/
│   └── test_suite.aether       # 14 tests cliniques validés (100% PASS)
└── docs/
    └── SCORES.md               # Références et interprétation clinique
```

---

## Résultats validés

### Stress test — 10 patients simultanés

| ID | Profil | NEWS2 | qSOFA | Verdict |
|---|---|---|---|---|
| 01 | Détresse respiratoire | 12 | 2 | 🔴 CRITIQUE |
| 02 | Post-opératoire stable | 0 | 0 | 🟢 STABLE |
| 03 | Confusion + fièvre | 12 | 3 | 🔴 CRITIQUE |
| 04 | Surveillance standard | 0 | 0 | 🟢 STABLE |
| 05 | Hypothermie + bradycardie | 14 | 2 | 🔴 CRITIQUE |
| 06 | Choc septique | 17 | 3 | 🔴 CRITIQUE |
| 07 | Patient stable | 0 | 0 | 🟢 STABLE |
| 08 | Fièvre modérée | 4 | 0 | 🟡 SEMI-URG |
| 09 | TA haute isolée | 1 | 0 | 🟡 SEMI-URG |
| 10 | Sepsis + tachycardie | 15 | 3 | 🔴 CRITIQUE |

### Vérification formelle des invariants : **14/14 ✓**

---

## Pourquoi Aether ?

Les règles cliniques ne tolèrent aucune ambiguïté. Aether impose :
- **`@invariant`** : chaque fonction certifie ses bornes (ex. NEWS2 ∈ [0,20])
- **`@example`** : cas de test intégrés au code, non séparables
- **Sandbox** : aucun effet de bord possible — pas de réseau, pas de fichier
- **Traçabilité** : chaque évaluation produit un log structuré auditable

Ces propriétés correspondent aux exigences de la norme **IEC 62304** (logiciels médicaux).

---

## ⚠️ Avertissement

Ce projet est un **démonstrateur technique**. Il ne remplace pas le jugement clinique d'un professionnel de santé qualifié. Ne pas utiliser comme seul outil de décision médicale.

---

## Auteur

Wilfred — construit avec [Aether v1.3](https://github.com/aether-lang) + Cowork (Anthropic)
