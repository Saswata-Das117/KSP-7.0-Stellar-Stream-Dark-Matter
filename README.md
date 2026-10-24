# Ripples in the Dark: Gaps in Stellar Streams and Predicted Dark Matter Subhalo Encounters

**KSP 7.0 project (Jun-Jul 2026), hosted by IIT Bombay**

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
├── notebooks/
    ├── 1.GD1 membership and gaps.ipynb              # GD-1 from Gaia DR3 + Monte Carlo + gap detection
    ├── 2.Multistream track and gaps.ipynb           # Same method on six streams
    └── 3.Stream ages and encounter rates.ipynb      # Stream ages + TNG50 subhalo encounter rates

```

Run the notebooks in order (1, 2, 3). Large inputs (Gaia, TNG50) are not committed; see [Data sources](#data-sources-and-references) and `data/` for access instructions.

## Notebook 1: GD-1 membership and gaps

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
- **Uncertainty:** a Monte Carlo over proper-motion errors (`mc_member_count`, 100 realizations) gives 643 (+35/-34) members, with 0 failed realizations. Gap detection is re-run in every realization.

**Candidate gaps** (`gap_recovery`):

| Gap centre (phi1) | Width | Nominal significance | MC recovery | Median significance | Centre scatter |
|---|---|---|---|---|---|
| -41.8 deg | 8.2 deg | 2.7 sigma | 93% | 2.65 | 0.65 deg |
| -20.8 deg | 7.5 deg | 4.0 sigma | 99% | 3.86 | 0.53 deg |

Note: the Monte Carlo member count sits below the nominal count partly because the catalogue proper motions already carry Gaia errors and are perturbed again (double noise), so the MC spread should be read as conservative.

## Notebook 2: multi-stream track and gaps

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

| Stream | Lu et al. age (Gyr) | Fitted age (Gyr) | Age used (Gyr) | Median rate (encounters per kpc) | Halos with encounters |
|---|---|---|---|---|---|
| Indus | 0.64 | 0.93 | 0.64 | 0.0077 | 51% |
| Jhelum | 0.14 | 0.21 | 0.14 | 0.0021 | 94% |
| NGC 6397 | 0.13 | 0.15 | 0.13 | 0 | 1% |
| Orphan | 1.90 | 1.74 | 1.90 | 0.0323 | 98% |
| Ylgr | none | 1.18 (0.54-2.58) | 1.18 | 0 | 34% |
| C-12 | none | 0.13 (0.07-0.22) | 0.13 | 0 | 6% |

Rates are in encounters per kpc of stream. A median rate of 0 means fewer than half of the host halos produce any encounter for that stream.

## Limitations and future work

- **No observed-vs-predicted test.** The detected gaps (Notebook 2) and predicted encounter rates (Notebook 3) are not compared statistically; this is future work.
- **No CDM vs. WDM test.** TNG50-1 is a cold dark matter simulation; a WDM prediction would need the subhalo mass function suppressed below a half-mode mass.
- **Detection completeness not measured.** Gap recovery is tested only under proper-motion-error perturbation (GD-1), not by injecting synthetic gaps.
- **Weak per-stream constraints.** Several streams have a median predicted rate of 0 and few detected gaps, and rates depend on the stream-age model, the 5 Gyr orbit integration and the Milky Way potential.
- **Catalogue dependence.** The six other streams use STREAMFINDER members, so its selection and completeness feed into the gap detection.

## Data sources and references

| Data | Source |
|---|---|
| GD-1 stars | Gaia DR3 via the ESA Gaia Archive (queried through TAP; the AIP mirror is the default, ARI Heidelberg and VizieR are fallbacks) |
| Other streams (Indus, C-12, Ylgr, Orphan, NGC 6397, Jhelum) | Member stars from the STREAMFINDER atlas of Ibata et al. (2024), ApJ 967, 89 ([DOI](https://doi.org/10.3847/1538-4357/ad382d)); machine-readable table: [apjad382dt1_mrt.txt](https://content.cld.iop.org/journals/0004-637X/967/2/89/revision1/apjad382dt1_mrt.txt) |
| Stream coordinate frames | `galstreams` Python package |
| Isochrones | PARSEC isochrones (Bressan et al. 2012) from the [CMD web interface](https://stev.oapd.inaf.it/cgi-bin/cmd), Gaia photometric system; 12 Gyr (log age = 10.079), [M/H] = -2.19, shifted to a distance modulus of 14.65 mag (about 8.5 kpc) |
| Subhalos and host halos | IllustrisTNG, run TNG50-1, snapshot 99 (z = 0), via the TNG API |

**TNG API access:** the notebook reads your personal key from an environment variable, so no credentials are stored in the repository. Get a key from the [TNG project site](https://www.tng-project.org/), then set `TNG_API_KEY` before running Notebook 3.

References:
- Stream discovery papers for the six streams (e.g. Shipp et al. 2019, Bonaca et al. 2019, Ibata et al. 2021)
- Fardal et al. (2015) for the particle-spray stream model, and Lu et al. (2026) for the length-matching age method
- Koposov et al. (2010) and Malhan & Ibata (2019) for GD-1 width cross-checks
- Ibata et al. (2024) for the STREAMFINDER stream catalogue
- Bressan et al. (2012) for the PARSEC isochrones

## Author

Saswata Das, 3rd-year BS-MS Physics, IISER Pune

## Acknowledgments

Mentors: Tamojeet Rowchoudhury (UC Berkeley) and Ravi Kumar (University of Minnesota). Coordinator: Vihang Vidwans (IIT Bombay).
