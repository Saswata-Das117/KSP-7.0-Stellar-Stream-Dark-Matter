# Ripples in the Dark: Gaps in Stellar Streams and Predicted Dark Matter Subhalo Encounters

**KSP 7.0 project, IISER Pune**

Dark matter subhalos passing near a stellar stream can carve gaps into it. This project (1) measures GD-1 from Gaia DR3 and detects candidate gaps with quantified uncertainty, (2) applies a comparable pipeline to six other streams, and (3) predicts how many subhalo encounters each stream should have experienced, using Milky Way-analog halos from IllustrisTNG50-1.

A formal observed-vs-predicted comparison (and a CDM vs. WDM test) is **not** part of this repository; see [Limitations and future work](#limitations-and-future-work).

## Headline results

- **GD-1:** 722 members from 3.0M Gaia DR3 stars; Monte Carlo over proper-motion errors gives 643 (+35/-34) members with 0 failed realizations. Two candidate gaps recovered in 93% and 99% of realizations.
- **Six other streams:** 13 candidate gaps in total; the strongest are in Indus (7.9 sigma) and C-12 (7.5 sigma).
- **Encounter-rate predictions:** per-stream orbits, ages and subhalo relative speeds give very different rates across streams (Orphan highest, NGC 6397 near zero), unlike a single-constant approach.

## Repository structure

```
KSP-7.0-GD-1-Stellar-Stream/
├── README.md
├── requirements.txt
├── LICENSE
├── notebooks/
│   ├── 01_gd1_characterization.ipynb        # GD-1 from Gaia DR3 + Monte Carlo + gap detection
│   ├── 02_multi_stream_underdensities.ipynb # Same method on six streams
│   └── 03_ages_and_encounter_rates.ipynb    # Stream ages + TNG50 subhalo encounter rates
├── data/                                    # small files + download instructions
├── results/                                 # CSV tables of the numbers below
├── figures/                                 # plots used in this README
└── archive/
    └── gd1_stellar_stream_midterm.ipynb     # superseded midterm draft
```

Run the notebooks in order (01, 02, 03). Large inputs (Gaia, TNG50) are not committed; see `data/` for download instructions.

## Notebook 1: GD-1 characterization

**Pipeline:** CMD/isochrone cut, proper-motion cut (GD1Koposov10 frame), spatial footprint cut, KDE density selection, iterative polynomial track fit, width cut, density profile, gap detection.

Run on the full footprint (25 RA/Dec tiles):

| Stage | Count |
|---|---|
| Raw Gaia DR3 stars | 3,012,234 |
| After CMD cut | 546,847 |
| After proper-motion cut | 20,855 |
| After footprint cut | 1,948 |
| **Final members (after width cut)** | **722** |

- Fitted stream width: sigma = 0.178 deg; the width cut is applied at 2 sigma = 0.356 deg.
- **Uncertainty:** a Monte Carlo over proper-motion errors (`mc_member_count`) gives 643 (+35/-34) members, with 0 failed realizations. Gap detection is re-run in every realization.

**Candidate gaps** (`gap_recovery`):

| Gap centre (phi1) | Width | Nominal significance | MC recovery | Median significance | Centre scatter |
|---|---|---|---|---|---|
| -41.8 deg | 8.2 deg | 2.7 sigma | 93% | 2.65 | 0.65 deg |
| -20.8 deg | 7.5 deg | 4.0 sigma | 99% | 3.86 | 0.53 deg |

Note: the Monte Carlo member count sits below the nominal count partly because the catalogue proper motions already carry Gaia errors and are perturbed again (double noise), so the MC spread should be read as conservative.

## Notebook 2: six other streams

The same selection, a sigma-clipped iterative track fit (polynomial order 3, MAD width estimate with a `curve_fit` fallback), and a multi-scale gap finder with edge exclusion and overlap-based deduplication ranked by significance.

| Stream | On track / input | Track width (deg) | Candidate gaps | Strongest gap |
|---|---|---|---|---|
| Indus-S19 | 869 / 1021 | 2.08 | 3 | 7.9 sigma |
| C-12-I24 | 876 / 929 | 1.37 | 3 | 7.5 sigma |
| Ylgr-I21 | 822 / 919 | 1.44 | 1 | 3.0 sigma |
| Orphan-K23 | 744 / 867 | 1.14 | 3 | 4.1 sigma |
| NGC6397-I21 | 677 / 691 | 2.55 | 2 | 2.2 sigma |
| Jhelum-a-B19 | 811 / 986 | 1.56 | 1 | 2.8 sigma |

13 candidate gaps in total. These are candidates from a density-based detector, not confirmed perturbations.

## Notebook 3: stream ages and encounter rates

- **Ages:** estimated by length matching with Fardal-spray models (following Lu et al. 2026), validated against published ages. Where no published age exists (Ylgr, C-12), the fitted age is used.
- **Encounter rates:** per-stream orbital shells from a 5 Gyr orbit integration, per-subhalo relative speeds from TNG, and the stream age above. 103 Milky Way-analog halos (1-2e12 Msun) from IllustrisTNG50-1, with subhalos of 1e6-1e9 Msun.

| Stream | Lu et al. age (Gyr) | Fitted age (Gyr) | Age used (Gyr) | Median rate | Halos with encounters |
|---|---|---|---|---|---|
| Indus | 0.64 | 0.93 | 0.64 | 0.0077 | 51% |
| Jhelum | 0.14 | 0.21 | 0.14 | 0.0021 | 94% |
| NGC 6397 | 0.13 | 0.15 | 0.13 | 0 | 1% |
| Orphan | 1.90 | 1.74 | 1.90 | 0.0323 | 98% |
| Ylgr | none | 1.18 (0.54-2.58) | 1.18 | 0 | 34% |
| C-12 | none | 0.13 (0.07-0.22) | 0.13 | 0 | 6% |

A median rate of 0 means fewer than half of the host halos produce any encounter for that stream.

## Limitations and future work

- **No formal observed-vs-predicted comparison.** Notebooks 2 and 3 are not combined into a statistical test. Detected gaps are candidates, and predicted rates are expected encounter counts; matching them quantitatively is future work.
- **No CDM vs. WDM discrimination.** TNG50-1 is a cold dark matter simulation. Testing WDM would require suppressing the subhalo mass function below a half-mode mass, which is not implemented.
- **Detection completeness is not measured.** Gap recovery is assessed under proper-motion-error perturbation (GD-1 only), not by injecting synthetic gaps.
- **Small-number effects.** Several streams have median predicted rate 0 and few detected gaps, so per-stream conclusions are weak.
- **Orbit and age assumptions.** Ages rely on a stream-length matching model; rates depend on the 5 Gyr orbit integration and the Milky Way potential used.

## Data and references

- Gaia DR3 (astrometry and photometry)
- IllustrisTNG50-1 (halos and subhalos)
- Stream discovery papers for the six streams (e.g. Shipp et al. 2019, Bonaca et al. 2019, Ibata et al. 2021) and Lu et al. (2026) for stream ages
- Koposov et al. (2010) and Malhan & Ibata (2019) for GD-1 width cross-checks

## Author

Saswata Das, 3rd-year BS-MS Physics, IISER Pune

## Acknowledgments

*[Add KSP mentor names here]*
