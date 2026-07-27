# Assumptions

> **Document:** `02_research/assumptions.md`
> **Project:** NagpurLens — Urban Intelligence Platform
> **Document Status:** Governance — every assumption below must remain traceable to `VISION.md`. No assumption may justify scope not already defined in Sections 3, 6, 7, or 8 of `VISION.md`.
> **Version:** v1.1
> **Last Updated:** July 2026
> **Owners:** Mohammad Ammar (Data Engineering, Analytics, Architecture), Mohammad Ruwaifa (Research, Data Collection, Verification), Zahid Khan (Documentation, Community)

---

## Changelog

**v1.1 (July 2026) — Governance Alignment Revision.** Targeted revision to bring this document into full alignment with `VISION.md`. A-021 reworded so six-decimal coordinate precision reflects Section 10's "precision appropriate for the data source" rather than a mandatory floor. A-001 reworded to remove ambiguity between "Nagpur district," "Nagpur," and "NMC jurisdiction" without redefining project geography. A-036 reframed as optional, source-contingent contextual metadata rather than a Version 1 dataset requirement. A-023–A-026 reframed as internal engineering implementation standards subordinate to `VISION.md`. Validation Matrix strengthened for GitHub Actions, source validation, and README validation coverage. No assumption IDs were added, removed, or renumbered. See Governance Change Log at the end of this document for the full itemized list.

---

## Purpose

NagpurLens converts fragmented civic data into verified, reproducible intelligence. That conversion is only as trustworthy as the assumptions it is built on — and every data project carries assumptions whether or not anyone writes them down. This document exists to write them down.

**Why assumptions are documented explicitly:**
An assumption that lives only in a contributor's head cannot be challenged, tested, or revised by anyone else. Writing it down turns a hidden belief into a claim that can be checked.

**Why explicit assumptions improve reproducibility:**
A developer reproducing the NagpurLens database from this repository alone (per `VISION.md` Section 7.3) needs to know not just *what* was collected but *what was presumed true* while collecting it. Reproducibility requires both the data and the reasoning behind it.

**Why assumptions reduce hidden bias:**
Undocumented assumptions are where bias hides — in which sources are trusted by default, in what "population" or "infrastructure" is taken to mean, in which localities are assumed comparable. Naming the assumption exposes it to scrutiny before it silently shapes a KPI.

**Why assumptions improve research quality:**
An assumption with a defined validation method forces the research process to ask "how would I know if I'm wrong?" before publishing a number. That question is what separates a verified dataset from a plausible-looking one.

**How assumptions guide later validation:**
Every assumption below feeds directly into `validation_report.md` and the CI test suite referenced in `VISION.md` Section 6. An assumption that cannot eventually be checked by a test, a cross-reference, or a field visit does not belong in this document — it belongs in `PROBLEM.md` as an open question.

---

## Assumption Lifecycle

```
Assumed
   ↓
Evidence Collected
   ↓
Validated
   ↓
Accepted / Rejected
   ↓
Updated
```

**Assumed** — The assumption is recorded here with `Status: Pending`, a stated rationale, and a defined validation method, before any dependent dataset or KPI is built on top of it.

**Evidence Collected** — The research or data collection team gathers the specific evidence named in the assumption's "Evidence Required" field (a cross-referenced source, a field check, a published dataset, a statistical test result).

**Validated** — The evidence is evaluated against the assumption's stated validation method. This step produces a pass/fail/partial outcome, not an opinion — the validation method is fixed at the time the assumption is written precisely so this step cannot be reinterpreted after the fact.

**Accepted / Rejected** — If validated, `Status` moves to `Accepted` and the assumption's `Confidence Level` is revisited (evidence typically raises confidence from Medium to High, or confirms a previously High rating). If the evidence contradicts the assumption, `Status` moves to `Rejected`, the assumption is struck through (not deleted — see Governance) and every downstream KPI, table, or document that depended on it is flagged in `PROBLEM.md` for re-evaluation.

**Updated** — Assumptions are living records. New evidence, a new source, or a changed civic reality (e.g., a newly opened metro line) can move a Rejected assumption back to Pending, or narrow an Accepted assumption's scope. Every update is versioned per the Maintenance Policy below.

---

## Assumption Categories

---

### 1. Project Assumptions

#### A-001 — Nagpur District Scope Boundary

**Category:** Project

**Statement:** NagpurLens Version 1 restricts all data collection, analysis, and KPI computation to the Nagpur geography defined in `VISION.md`. `VISION.md` refers to this geography variously as "Nagpur," "Nagpur district," and "Nagpur Municipal Corporation (NMC) jurisdiction" (Sections 2 and 4); this assumption does not resolve that terminology into a boundary stricter or narrower than `VISION.md` itself establishes. NMC jurisdiction is used as the working administrative reference for identifying which localities fall within the project's scope, consistent with `VISION.md`'s repeated framing of NMC as the relevant civic body (Section 2) — not as a redefinition of project geography.

**Rationale:** VISION.md Section 7.6 ("Depth Before Breadth") requires Version 1 to be fully correct before any geographic expansion. A bounded scope makes exhaustive source verification achievable with the current team size. This assumption exists to operationalize, not narrow, whatever boundary `VISION.md` sets — if `VISION.md`'s geographic scope is clarified or revised, this assumption is updated to match rather than treated as an independent source of truth.

**Evidence Required:** NMC's published administrative boundary/ward map confirming which localities fall under its jurisdiction.

**Validation Method:** Cross-check every locality in the localities table against the official NMC ward list; flag any locality outside the boundary for removal or reclassification.

**Primary Sources:** Government/municipal boundary publications (see `source_registry.csv`, category: Administrative Boundaries).

**Confidence Level:** High

**Status:** Pending

**Risk if False:** Localities outside NMC jurisdiction could be included, producing KPIs that misrepresent the "Nagpur" the platform claims to describe.

**Mitigation:** Any locality without a confirmed NMC boundary match is excluded from v1.0 and logged as a candidate for future scope review, not silently included.

---

#### A-002 — Evidence-Driven Design Is Achievable

**Category:** Project

**Statement:** Sufficient public, official, and field-verifiable evidence exists to answer the three questions defined in VISION.md Section 3 without relying on unverifiable broker or word-of-mouth data.

**Rationale:** The project's stated purpose (VISION.md, opening quote) is to replace rumor-based real estate decisions with data. If sufficient evidence does not exist, the project's core premise fails.

**Evidence Required:** A populated `source_registry.csv` showing at least one named, dated, public source for each of the six KPI inputs.

**Validation Method:** Audit the source registry against the KPI list in VISION.md Section 4; any KPI input lacking a named source is flagged before that KPI is marked "live."

**Primary Sources:** Full breadth of `sources.md` categories — government, open data, field verification.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A KPI could ship with an unverifiable input, undermining VISION.md Section 7.1 ("Trust Before Analytics").

**Mitigation:** A KPI whose inputs cannot be fully sourced is deferred to a later version rather than shipped with a gap, per the version-gating already defined in VISION.md Section 4.

---

#### A-003 — Data Quality Outweighs Dataset Size

**Category:** Project

**Statement:** A smaller, fully verified dataset (50+ localities, 150+ infrastructure records, per VISION.md Section 10) produces more reliable KPIs than a larger, unverified one.

**Rationale:** VISION.md Section 7.1 states an insight built on unverified data "is a guess with a chart on top." The project deliberately trades breadth for verifiability.

**Evidence Required:** Comparison of validation pass rates on the current scoped dataset versus estimates of unverified record volume available from bulk scraping.

**Validation Method:** Track the ratio of "verified" to "collected" records at each pipeline stage; the project accepts a lower total record count if it keeps this ratio near 100%.

**Primary Sources:** Internal validation logs (`validation_report.md`).

**Confidence Level:** High

**Status:** Pending

**Risk if False:** If verification overhead becomes so costly that the dataset stays too small to be statistically meaningful, the KPIs lose analytical value even though they remain "true."

**Mitigation:** Minimum viable dataset thresholds (50+ localities, 150+ infrastructure records) are fixed as the Version 1 floor in VISION.md Section 10; if verification cannot reach that floor within a reasonable timeframe, scope (not standards) is revisited.

---

#### A-004 — Reproducibility Is Achievable With Available Tooling

**Category:** Project

**Statement:** The complete database, KPI scores, and analytical outputs can be reproduced by a developer with no prior project context, using only this repository, MySQL, Python/Pandas, and standard open-source tooling.

**Rationale:** VISION.md Section 7.3 makes this a non-negotiable success criterion.

**Evidence Required:** A clean-environment test: cloning the repository on a machine with no prior setup and executing the documented pipeline end to end.

**Validation Method:** Run the full pipeline (schema creation → data load → KPI computation) in a fresh Docker container or clean virtual machine and diff the output against the published dataset.

**Primary Sources:** Internal repository structure and CI logs.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A reproducibility claim that fails under audit damages the credibility the entire project is built on.

**Mitigation:** Reproducibility is tested via GitHub Actions CI on every push (VISION.md Section 6), not asserted once and left unverified.

---

#### A-005 — Version Sequencing Reflects Real Dependency, Not Just Preference

**Category:** Project

**Statement:** Version 2 (growth signals) and Version 3 (investment scoring) genuinely require Version 1's infrastructure and population data to be complete and validated first — the sequencing in VISION.md Section 8 is a technical dependency, not an arbitrary rollout order.

**Rationale:** GSI and IAS (VISION.md Section 4, KPIs 5–6) are explicitly defined as composites of the Version 1 KPIs. If Version 1 data changes materially after Version 2 ships, every downstream score would need recomputation.

**Evidence Required:** A dependency map showing which Version 1 tables and KPIs feed directly into each Version 2/3 KPI formula.

**Validation Method:** Before starting Version 2 work, confirm in `kpi_definitions.md` that GSI's formula only references fields already finalized in the Version 1 schema.

**Primary Sources:** `kpi_definitions.md`, internal schema documentation.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** Starting Version 2 work before Version 1 is stable risks rebuilding growth signal calculations from scratch.

**Mitigation:** VISION.md Section 7.6 formally blocks starting a new version until the prior one is validated and documented.

---

### 2. Research Assumptions

#### A-006 — Government Publications Are the Authoritative Baseline

**Category:** Research

**Statement:** Data published directly by government bodies (NMC, Census of India, Maharashtra state departments, Smart City Nagpur Limited) is treated as the highest-authority source for any fact it covers, ahead of media reporting, real estate listings, or crowd-sourced platforms.

**Rationale:** Government sources carry legal and administrative accountability that secondary sources do not.

**Evidence Required:** A documented publisher and publication/update date for each government source cited.

**Validation Method:** Every fact sourced from a government publication is tagged in the source registry with an "authority tier"; conflicting non-government sources are logged but do not override the government figure without a documented reason.

**Primary Sources:** NMC, Census of India, Smart City Nagpur Limited, Maharashtra state open data portals.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** A government dataset could itself be outdated (as already acknowledged for Census 2011 in VISION.md Section 7.4) or contain administrative errors.

**Mitigation:** Authority tier determines default trust, but VISION.md Section 7.4 already requires known limitations (like Census age) to be stated openly rather than treated as settled fact.

---

#### A-007 — Academic Literature Provides Methodological, Not Factual, Guidance

**Category:** Research

**Statement:** Peer-reviewed urban studies and geospatial research literature are used to validate methodology (e.g., how infrastructure density indices are typically constructed) but are not used as a source of Nagpur-specific facts unless the study collected Nagpur-specific primary data.

**Rationale:** Keeps the project's factual claims grounded in Nagpur-specific evidence while still benefiting from established analytical frameworks.

**Evidence Required:** Citation trail distinguishing "methodology reference" from "factual source" in the source registry.

**Validation Method:** Any academic citation is checked against a two-question test: (1) Does it inform a formula or approach? (2) Does it assert a Nagpur-specific fact? Only sources answering "yes" to (2) with primary data collection are logged as factual sources.

**Primary Sources:** Peer-reviewed urban informatics and geospatial science literature.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** Borrowing a generic finding from a different city's study and applying it to Nagpur without local verification.

**Mitigation:** Methodology references are clearly separated from evidentiary sources throughout the documentation.

---

#### A-008 — Every Public Claim Requires a Named Citation

**Category:** Research

**Statement:** No number, ranking, or claim published by NagpurLens (in the dashboard, README, or reports) appears without a traceable source in `sources.md` or `source_registry.csv`.

**Rationale:** Directly implements VISION.md Section 7.7 ("Evidence Before Claims").

**Evidence Required:** A source ID linked to every published figure.

**Validation Method:** An automated or manual documentation check that scans published outputs for figures lacking an adjacent source reference.

**Primary Sources:** `source_registry.csv` in full.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** An uncited figure undermines the project's core differentiator from "black-box" projects (VISION.md Section 5).

**Mitigation:** Any figure that cannot be traced to a source is withheld from publication until sourced.

---

#### A-009 — AI Assistance Cannot Substitute for Primary Evidence

**Category:** Research

**Statement:** Large language models or AI tools may be used to draft documentation, summarize sources, or suggest analytical approaches, but may never be treated as a source of factual claims about Nagpur (e.g., a locality's population or infrastructure count).

**Rationale:** AI-generated facts are not traceable to a verifiable origin and would violate VISION.md Section 7.7 by definition.

**Evidence Required:** A documented distinction, in contributor guidelines, between AI-assisted drafting and AI-sourced facts.

**Validation Method:** Any fact appearing in project documentation is checked against a named, human-verifiable source before publication, regardless of whether AI tools were used in drafting the surrounding text.

**Primary Sources:** N/A — this assumption governs process, not data.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** A plausible-sounding but fabricated fact could enter the dataset if this boundary is not enforced.

**Mitigation:** Peer review (per the pipeline in VISION.md Section 6) explicitly checks for unsourced factual claims before merge.

---

#### A-010 — Multiple Independent Sources Improve Confidence

**Category:** Research

**Statement:** Where two or more independent sources (e.g., OpenStreetMap and Google Maps, per VISION.md Section 4) agree on a fact, confidence in that fact is higher than if only one source reports it.

**Rationale:** Cross-referencing is the primary verification method described for the localities table in VISION.md Section 4.

**Evidence Required:** At least two independent geographic references per locality, where available.

**Validation Method:** Coordinate agreement within a defined tolerance (documented in the verification methodology) across independent sources raises a record's confidence tier; disagreement triggers manual field or source review.

**Primary Sources:** OpenStreetMap, Google Maps, official mapping resources.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** Two sources could share a common upstream error (e.g., both scraping the same outdated municipal map), creating false confidence.

**Mitigation:** Where possible, at least one source per locality is an official/government reference rather than two crowd-sourced platforms alone.

---

### 3. Source Assumptions

#### A-011 — Registered Sources Remain Publicly Accessible

**Category:** Source

**Statement:** Sources listed in `sources.md` and `source_registry.csv` will remain publicly accessible at their documented URLs or through their documented access method for the working life of the current project version.

**Rationale:** Reproducibility (VISION.md Section 7.3) depends on a future developer being able to reach the same sources used originally.

**Evidence Required:** Access date and URL/reference recorded for every source.

**Validation Method:** Periodic link-check pass (see Maintenance Policy) confirming each registered source is still reachable.

**Primary Sources:** N/A — governs the registry itself.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Government portals and open data pages are periodically restructured or removed, breaking reproducibility.

**Mitigation:** Where licensing permits, an archived copy or snapshot reference (e.g., Wayback Machine link) is recorded alongside the live URL.

---

#### A-012 — Source Registry Is Complete and Authoritative

**Category:** Source

**Statement:** `source_registry.csv` contains every source actually used to populate the database — no data point exists in the tables without a corresponding registry entry.

**Rationale:** A dataset with unregistered sources cannot be fully audited, violating VISION.md Section 5's "every source named and dated" standard.

**Evidence Required:** A one-to-one mapping between data table fields and registry entries.

**Validation Method:** An automated check (part of the CI validation suite) that flags any record without a linked `source_id`.

**Primary Sources:** N/A — internal consistency check.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** An unregistered data point would be indistinguishable from an unverifiable guess.

**Mitigation:** The `source_id` field is enforced as non-nullable in the database schema.

---

#### A-013 — Official Portals Publish Authentic Data

**Category:** Source

**Statement:** Data published on official government or NMC domains is assumed to genuinely originate from that authority and has not been tampered with in transit.

**Rationale:** Verifying authenticity independently for every government dataset is not practically feasible for a student-led project; the project instead relies on domain authority and HTTPS/official hosting as a baseline trust signal.

**Evidence Required:** Confirmation that the source domain is an official government (.gov.in, nic.in, or NMC-verified) domain.

**Validation Method:** Domain and publisher verification recorded in the source registry for every government-tagged source.

**Primary Sources:** NMC, Government of Maharashtra, Census of India official domains.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** A spoofed or unofficial mirror site could be mistaken for an official source.

**Mitigation:** Only sources reached through verified official domains, or through recognized open-data aggregators (e.g., data.gov.in), are tagged as "government authoritative."

---

#### A-014 — Dataset Licenses Permit Research and Redistribution Use

**Category:** Source

**Statement:** Datasets registered in the source registry carry a license (open government data license, public domain, or similar) that permits their use in an open-source research and educational platform, including redistribution of derived KPIs.

**Rationale:** VISION.md positions NagpurLens as open-source; using data the project cannot legally redistribute would undermine that model.

**Evidence Required:** A recorded license type for every source in the registry.

**Validation Method:** No source is added to the registry without a documented license field; sources with unclear or restrictive licenses are used for internal validation only, not redistributed in derived form.

**Primary Sources:** Government open data licenses, Census of India terms of use.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Publishing derived data from a restrictively licensed source could create legal exposure for the project and contributors.

**Mitigation:** A license audit is part of the source registry review cycle (see Governance section).

---

#### A-015 — Archived Datasets Remain Available for Long-Term Reproducibility

**Category:** Source

**Statement:** Where an original source dataset is later removed or altered, a locally archived or otherwise preserved copy remains available so historical KPI computations remain reproducible.

**Rationale:** Long-term auditability (VISION.md Section 7.3) requires that a dataset removed from the web in 2028 doesn't invalidate a KPI published in 2026.

**Evidence Required:** A local or version-controlled snapshot of source data where license terms permit storage.

**Validation Method:** Confirm that raw source files (not just derived tables) are retained in the repository's data directory or a linked archive, for every license-permitting source.

**Primary Sources:** N/A — internal archival practice.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A KPI could become unreproducible the moment its underlying government page changes or disappears.

**Mitigation:** Raw source snapshots are committed alongside processed data wherever licensing allows; where it does not, the access date and method are documented in maximum available detail.

---

### 4. Data Collection Assumptions

#### A-016 — Government Datasets Reasonably Represent Ground Reality

**Category:** Data Collection

**Statement:** Government-published datasets (population counts, facility lists, boundary maps) are accurate representations of the real-world state at their time of publication, within normal administrative reporting error.

**Rationale:** The project cannot independently re-survey all of Nagpur; it relies on official records as a reasonable proxy for reality, while documenting known gaps.

**Evidence Required:** Consistency checks between government data and independent field or crowd-sourced verification where feasible.

**Validation Method:** Spot-check a sample of government-listed facilities (e.g., schools, hospitals) against independent map sources; log the discrepancy rate.

**Primary Sources:** NMC, Census of India, department-level facility directories.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Administrative records can lag real-world changes (a closed school still listed, a new hospital not yet registered).

**Mitigation:** Discrepancy rates found during spot-checks are documented in `validation_report.md` as a stated limitation, per VISION.md Section 7.4.

---

#### A-017 — Published Infrastructure Locations Are Correctly Geocoded

**Category:** Data Collection

**Statement:** Where a government or open-data source publishes coordinates for a hospital, school, or other facility, those coordinates correctly correspond to the facility's real-world location, within the precision stated by the source.

**Rationale:** The Haversine locality-assignment validation described in VISION.md Section 4 depends on input coordinates being reasonably accurate.

**Evidence Required:** Coordinate precision metadata from the originating source (where available).

**Validation Method:** GPS bounds testing (confirming coordinates fall within Nagpur district's known geographic envelope) as part of the validation suite; visibly incorrect coordinates (e.g., falling in another state) are flagged and corrected or excluded.

**Primary Sources:** Government facility directories, OpenStreetMap, Google Maps.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A misplaced coordinate could assign a facility to the wrong locality, corrupting IDI, PIR, and HAS for two localities at once.

**Mitigation:** Every infrastructure record's locality assignment is verified via the documented Haversine nearest-centroid method (VISION.md Section 4); orphan or out-of-bounds records are excluded rather than force-assigned.

---

#### A-018 — Administrative and Ward Boundaries Remain Stable Within a Version Cycle

**Category:** Data Collection

**Statement:** NMC ward and locality boundaries do not change materially during the active development window of a given NagpurLens version.

**Rationale:** Boundary changes mid-collection would invalidate locality assignments collected earlier in the same cycle.

**Evidence Required:** Confirmation of the boundary publication date used and any known scheduled boundary revisions (e.g., post-delimitation changes).

**Validation Method:** Check for any NMC or state government boundary revision notices during the data collection window; re-validate affected localities if a change is announced.

**Primary Sources:** NMC administrative notifications, state delimitation records.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A mid-cycle boundary redraw could silently shift which locality a record belongs to.

**Mitigation:** The locality ID standard (VISION.md Section 4) is versioned; a boundary change triggers a documented locality ID revision rather than a silent overwrite.

---

#### A-019 — Property Registration Records, Where Obtainable, Are Usable at Aggregate Level

**Category:** Data Collection

**Statement:** Where property registration value data can be legally obtained at a locality or ward aggregate level, it is usable as a market trend indicator without requiring individual transaction-level detail.

**Rationale:** Individual transaction records may carry privacy or access restrictions; aggregate figures avoid that issue while still supporting the Real Estate Assumptions in Section 8.

**Evidence Required:** Confirmation that any registration data used is published at aggregate (locality/ward/year) level, not individual-transaction level.

**Validation Method:** Source registry review confirming the granularity of any registration-derived dataset before use.

**Primary Sources:** Maharashtra Department of Registration and Stamps, IGR Maharashtra published statistics.

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** Locality-level registration data may not be publicly published at all, limiting Version 3's Investment Attractiveness Score inputs.

**Mitigation:** If aggregate registration data is unavailable, IAS (v3.0) documents this as a known input gap rather than substituting an unverified estimate.

---

#### A-020 — Census 2011 Remains the Best Available Population Baseline for Version 1

**Category:** Data Collection

**Statement:** In the absence of a published, locality-level Census 2021/2027 dataset, Census 2011 figures remain the most defensible population baseline available for Version 1 KPIs (PIR, in particular).

**Rationale:** VISION.md Section 7.4 already flags this as a stated limitation rather than a hidden one; this assumption formalizes why 2011 data is used despite its age.

**Evidence Required:** Confirmation that no more recent official, locality-level population count exists for Nagpur.

**Validation Method:** A documented search of Census of India and state statistical department publications for any newer locality-level release; re-run before each version milestone.

**Primary Sources:** Census of India 2011 district handbook, Nagpur.

**Confidence Level:** High (regarding the absence of newer data), Low (regarding current accuracy of the 15-year-old baseline itself)

**Status:** Pending

**Risk if False:** Population figures could understate real growth in fast-developing peripheral localities, distorting PIR.

**Mitigation:** Every PIR output is labeled with the Census year used, and the limitation is stated directly alongside the metric, per VISION.md Section 7.4 — not buried in a footnote.

---

#### A-021 — Coordinate Precision Is Sufficient for Locality-Level (Not Parcel-Level) Analysis

**Category:** Data Collection

**Statement:** Coordinates are stored at up to six decimal places where the originating source supports that precision (VISION.md Section 4), but six decimals is a ceiling, not a mandatory floor — validation follows the precision actually supported by each original source, per VISION.md Section 10's "precision appropriate for the data source." At whatever precision is recorded and documented, coordinates are sufficient for locality-level KPIs (IDI, MII) but are not claimed to be sufficient for individual land-parcel-level analysis.

**Rationale:** Sets an honest precision boundary consistent with VISION.md Section 10's phrase "precision appropriate for the data source." Section 4's "6 decimal places" describes the precision available from strong sources, not a requirement every source must meet before a record can be accepted.

**Evidence Required:** Documented precision/accuracy metadata from each coordinate source, including cases where a source supports fewer than six decimal places.

**Validation Method:** GPS bounds and precision checks as part of the validation suite; each record's precision is evaluated against its own source's stated capability rather than a fixed six-decimal requirement, and any record whose documented precision falls below locality-level usability is flagged.

**Primary Sources:** OpenStreetMap, Google Maps, official mapping resources.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** Overstating precision could mislead a user into treating locality-level scores as parcel-specific investment advice.

**Mitigation:** Documentation and dashboard copy explicitly state that KPIs are locality-level indicators, not property-specific valuations.

---

### 5. Data Engineering Assumptions

#### A-022 — Datasets Can Be Reliably Joined via the Locality ID Standard

**Category:** Data Engineering

**Statement:** The XXX-format, alphabetically assigned locality ID (VISION.md Section 4) is sufficient as a stable join key across the localities table, infrastructure table, and all derived KPI tables.

**Rationale:** A consistent join key across every table and document is explicitly required by VISION.md Section 4.

**Evidence Required:** Referential integrity checks showing zero orphan `locality_id` values across all tables.

**Validation Method:** Automated foreign-key/join integrity tests in the CI validation suite (VISION.md Section 10).

**Primary Sources:** N/A — internal schema design.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** A broken join key would silently drop or misattribute records across tables.

**Mitigation:** Locality ID assignment is centralized in one canonical table; all other tables reference it by foreign key, enforced at the database level.

---

#### A-023 — Coordinate Reference Systems Can Be Standardized Across Sources

**Category:** Data Engineering

**Statement:** Coordinates collected from different sources (OpenStreetMap, Google Maps, government portals) can be normalized to a single consistent geographic coordinate system (WGS84) without meaningful loss of accuracy.

**Rationale:** Haversine distance validation (VISION.md Section 4) requires all coordinates to share a common reference system. This is an internal engineering implementation standard that supports that requirement — it does not itself expand what VISION.md defines as the Version 1 dataset or KPI set.

**Evidence Required:** Confirmation of the native CRS used by each source.

**Validation Method:** CRS metadata check and conversion step during data ingestion; converted coordinates are spot-checked against the original source's map display.

**Primary Sources:** OpenStreetMap, Google Maps, government mapping resources.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** A CRS mismatch could introduce systematic coordinate drift.

**Mitigation:** All ingestion pipelines standardize to WGS84 (EPSG:4326) at the point of entry, with conversion logic documented in the data dictionary.

---

#### A-024 — Missing Values Can Be Reliably Identified

**Category:** Data Engineering

**Statement:** Null, blank, and placeholder values (e.g., "N/A", "-999", empty strings) across all source formats can be reliably detected and distinguished from legitimate zero values during ingestion.

**Rationale:** Null-check validation is an explicit success metric in VISION.md Section 10. This assumption describes the internal engineering standard used to satisfy that metric; it implements Section 10's requirement rather than adding a requirement of its own.

**Evidence Required:** A documented list of null/placeholder conventions observed across all source formats used.

**Validation Method:** Automated null-check tests run in CI on every push, per VISION.md Section 10.

**Primary Sources:** N/A — internal data engineering practice.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** An unrecognized placeholder value (e.g., "0" meaning "unknown" rather than "zero facilities") could silently corrupt a KPI calculation.

**Mitigation:** Each source's specific null/placeholder conventions are documented per-source in the data dictionary before ingestion.

---

#### A-025 — Duplicate Records Can Be Reliably Detected

**Category:** Data Engineering

**Statement:** Duplicate infrastructure or locality records — arising from overlapping sources describing the same real-world entity — can be identified using a combination of name-matching and coordinate-proximity checks.

**Rationale:** Zero orphan and non-duplicated records are implied requirements of the "150+ infrastructure records" success metric in VISION.md Section 10 being a meaningful, non-inflated count. This is an internal engineering implementation standard in service of that metric, not an independent project requirement.

**Evidence Required:** A defined proximity and name-similarity threshold for flagging likely duplicates.

**Validation Method:** Automated duplicate-detection pass during validation, with manual review of flagged pairs before merge or exclusion.

**Primary Sources:** N/A — internal validation logic, applied across all infrastructure sources.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Undetected duplicates would artificially inflate IDI and HAS for affected localities.

**Mitigation:** Any flagged duplicate is manually reviewed and resolved before being counted in the published record total.

---

#### A-026 — Schema Evolution Can Be Managed Without Breaking Reproducibility

**Category:** Data Engineering

**Statement:** As new tables, fields, or KPIs are added in later versions (v2.0, v3.0), the schema can evolve without invalidating previously published Version 1 data or breaking existing reproducibility guarantees.

**Rationale:** VISION.md Section 7.6 requires later versions to build on, not disturb, the validated Version 1 foundation. This assumption is an internal engineering implementation standard that operationalizes that principle at the schema level; it governs how migrations are executed, not what the roadmap or scope is.

**Evidence Required:** A documented schema migration and versioning strategy.

**Validation Method:** Schema migrations are version-tagged and tested against the full CI validation suite before merge, confirming Version 1 outputs remain unchanged.

**Primary Sources:** N/A — internal schema governance.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A careless migration could silently alter a Version 1 KPI while adding Version 2 features.

**Mitigation:** Migrations are additive by default (new tables/columns) rather than destructive; any breaking change requires an explicit version bump and changelog entry.

---

### 6. Geospatial Assumptions

#### A-027 — All Coordinates Use a Standard Geographic System

**Category:** Geospatial

**Statement:** All coordinates in the database are stored and interpreted in decimal-degree WGS84 format, consistently across every table.

**Rationale:** Consistency is required for the Haversine validation described in VISION.md Section 4 to produce meaningful, comparable distances.

**Evidence Required:** Schema-level documentation confirming coordinate format and system for every geospatial field.

**Validation Method:** Format and range validation as part of the CI test suite (values must fall within valid latitude/longitude ranges for Nagpur district).

**Primary Sources:** N/A — internal schema standard, tied to source CRS documentation (A-023).

**Confidence Level:** High

**Status:** Pending

**Risk if False:** Mixed coordinate systems (e.g., a stray UTM value) would silently break every distance-based KPI.

**Mitigation:** Coordinate format is enforced at the database schema level with range constraints.

---

#### A-028 — Haversine Nearest-Centroid Spatial Joins Are Reliable for Locality Assignment

**Category:** Geospatial

**Statement:** Assigning each infrastructure record to its nearest locality centroid using the Haversine formula produces correct locality assignment for the large majority of records.

**Rationale:** This is the explicit validation method named in VISION.md Section 4 for achieving "zero orphan records."

**Evidence Required:** A sample of manually-verified locality assignments to compare against the automated Haversine result.

**Validation Method:** Manual spot-check of a statistically meaningful sample of automated assignments; discrepancy rate documented in `validation_report.md`.

**Primary Sources:** N/A — internal geospatial method, applied to sourced coordinate data.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Records near a locality boundary could be misassigned to a neighboring locality, especially where localities are geographically small or irregularly shaped.

**Mitigation:** Records falling within a defined distance threshold of two locality centroids are manually reviewed rather than auto-assigned.

---

#### A-029 — Straight-Line Distance Calculations Are Meaningful Proxies for Accessibility

**Category:** Geospatial

**Statement:** Straight-line (Haversine) distance to the nearest metro station is a reasonable proxy for real-world accessibility for the purposes of the Metro Influence Index (MII), even though it does not account for road network routing.

**Rationale:** Road-network-based routing data (e.g., via a routing API) is a heavier data dependency than Version 1 scope supports; straight-line distance is a documented simplification.

**Evidence Required:** A comparison, on a sample of localities, between straight-line and actual road-network distance to the nearest metro station.

**Validation Method:** Spot-check sample comparison; if straight-line and road distance diverge significantly for a meaningful share of localities, this is documented as a stated MII limitation.

**Primary Sources:** N/A — internal geospatial method.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A locality separated from the metro by a river or lack of direct road could show an artificially favorable MII score.

**Mitigation:** MII's methodology explicitly documents straight-line distance as its basis, per VISION.md Section 7.4's transparency-about-limitations principle; road-network-based routing is logged as a future enhancement, not silently assumed equivalent.

---

#### A-030 — Administrative Boundary Definitions Are Internally Consistent

**Category:** Geospatial

**Statement:** The locality boundaries used for area calculations (feeding IDI's per-km² measure) are drawn from a single consistent boundary source, not mixed from multiple conflicting boundary definitions.

**Rationale:** Mixing boundary definitions (e.g., a locality's colloquial extent versus its official ward extent) would make area-based KPIs like IDI incomparable across localities.

**Evidence Required:** A single documented boundary source used consistently for every locality's area calculation.

**Validation Method:** Schema and source-registry check confirming one boundary source per locality, documented and consistent project-wide.

**Primary Sources:** NMC ward boundary maps, official GIS resources where available.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Inconsistent boundary sources would make IDI comparisons across localities misleading.

**Mitigation:** Where an official GIS boundary is unavailable for a locality, this gap is documented and the locality's IDI is flagged with a lower confidence tier rather than estimated from an inconsistent source.

---

#### A-031 — GPS Coordinates Fall Within Known Geographic Bounds for Nagpur District

**Category:** Geospatial

**Statement:** All valid coordinate records in the database fall within the known latitude/longitude envelope of Nagpur district.

**Rationale:** This is the explicit "GPS bounds test" success metric named in VISION.md Section 10.

**Evidence Required:** The documented latitude/longitude bounding box for Nagpur district.

**Validation Method:** Automated bounds-check test in the CI validation suite, run on every push.

**Primary Sources:** Government district boundary data, OpenStreetMap.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** A data entry or geocoding error could place a record outside Nagpur entirely without detection.

**Mitigation:** Bounds violations fail the CI pipeline automatically and block merge until corrected.

---

### 7. Infrastructure Assumptions

#### A-032 — Published Metro Stations Are Operational at Time of Recording

**Category:** Infrastructure

**Statement:** Metro stations recorded in the infrastructure table are confirmed operational (not under construction or planned) as of their recorded collection date.

**Rationale:** MII (VISION.md Section 4) is defined against "the nearest operational metro station" specifically — a planned station would misrepresent current accessibility.

**Evidence Required:** Official Nagpur Metro (Maha Metro) operational status confirmation for each recorded station.

**Validation Method:** Cross-reference each recorded station against the current Maha Metro operational line map at time of data collection; re-verify at each version milestone.

**Primary Sources:** Maha Metro official publications.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** Including a planned-but-not-yet-open station would overstate MII for nearby localities.

**Mitigation:** Station operational status and the date it was verified are both recorded fields, not assumed static.

---

#### A-033 — Road and Highway Network Data, Where Used, Reflects Current Layout

**Category:** Infrastructure

**Statement:** Any road or highway data referenced (for context or future routing enhancements) reflects the current, not historical or planned, road network.

**Rationale:** Using outdated road layouts could misinform any future accessibility calculation built on top of Version 1 data.

**Evidence Required:** Publication/update date for any road network dataset used.

**Validation Method:** Date-stamp check against the source registry; datasets older than a defined threshold are flagged for re-verification.

**Primary Sources:** OpenStreetMap, National Highways Authority of India (NHAI), state PWD publications.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Newer roads (particularly in fast-developing peripheral localities, per VISION.md Section 2) may be missing from older datasets.

**Mitigation:** Road network data currency is documented as a stated limitation where recency cannot be fully confirmed.

---

#### A-034 — School and Hospital Directories Are Reasonably Complete for Their Category

**Category:** Infrastructure

**Statement:** Government or open-data school and hospital directories used for the infrastructure table capture the large majority of operating facilities in a given locality, even if not literally every single one.

**Rationale:** HAS (Healthcare Accessibility Score) and general infrastructure counts depend on directories being broadly complete, not exhaustive to the last unregistered clinic.

**Evidence Required:** Cross-reference of a sample locality's directory-listed facilities against an independent map search.

**Validation Method:** Spot-check comparison; completeness gap documented per category if found.

**Primary Sources:** Department of Health, Department of Education facility directories; OpenStreetMap points of interest.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Small, unregistered, or newly opened facilities could be systematically under-counted, understating HAS for newer localities.

**Mitigation:** Known completeness gaps are stated per VISION.md Section 7.4, and unregistered/informal facilities are explicitly out of scope rather than silently assumed captured.

---

#### A-035 — Utility (Water/Sewer) Infrastructure Data Has Limited Public Availability

**Category:** Infrastructure

**Statement:** Locality-level water supply and sewerage infrastructure data is only partially available through public sources, and Version 1 does not claim comprehensive utility coverage.

**Rationale:** Utility network data is often held internally by municipal engineering departments rather than published openly, unlike the nine infrastructure categories named in VISION.md Section 4.

**Evidence Required:** A documented search confirming what, if any, utility data is publicly available for Nagpur.

**Validation Method:** Source registry audit; utility infrastructure is only added to the core dataset if a genuinely public, locality-level source is found.

**Primary Sources:** NMC water supply department, Maharashtra Jeevan Pradhikaran, where publicly available.

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** N/A — this assumption itself documents a known gap rather than asserting availability; the primary risk is scope creep if utility data is assumed available where it is not.

**Mitigation:** Utility infrastructure remains outside the nine core Version 1 infrastructure categories unless and until a public source is confirmed.

---

#### A-036 — Commercial and Industrial Hub Classification Is Stable Enough to Record

**Category:** Infrastructure

**Statement:** Zoning context — a locality's classification as a commercial hub, industrial area, or primarily residential zone — is optional contextual metadata, not a required field of the Version 1 Core Dataset defined in VISION.md Section 4. It is recorded only where an official source (e.g., the NMC Development Plan or NIT records) already publishes the classification; it is never independently collected or researched as a standalone Version 1 deliverable, and its presence or absence never expands the locality or infrastructure tables' defined scope. Where recorded, it is treated as stable enough within a version cycle to aid interpretation of already-computed KPIs, not as an input to any KPI formula defined in VISION.md Section 4.

**Rationale:** This is a subordinate, implementation-level convenience: where zoning context is already published, it can help explain a KPI result (e.g., why a commercial-heavy locality shows a different infrastructure profile than a residential one). It exists in service of interpreting the Version 1 KPIs (VISION.md Section 4), not as a new dataset requirement, and does not alter the roadmap sequencing in VISION.md Section 8.

**Evidence Required:** NMC zoning classification or master plan designation, where already published, for the localities it happens to cover — not a project mandate to source it for every locality.

**Validation Method:** Where a zoning attribute is recorded, cross-reference it against the NMC master development plan's zoning maps; absence of a recorded value for a locality is not treated as a validation failure.

**Primary Sources:** NMC Development Plan, Nagpur Improvement Trust (NIT) records.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Rezoning or informal land-use change could make a recorded classification stale.

**Mitigation:** Zone classification is recorded with its source publication date; re-verified at each version milestone rather than treated as permanent.

---

#### A-037 — Government Office Locations Are Relatively Permanent

**Category:** Infrastructure

**Statement:** Government office locations (NMC ward offices, police stations, fire stations) change location infrequently enough that a recorded coordinate remains valid for the duration of a version cycle.

**Rationale:** Unlike commercial establishments, government facilities are assumed to have low relocation frequency, supporting their inclusion as a relatively stable infrastructure category.

**Evidence Required:** Confirmation of current operating address from official government directories.

**Validation Method:** Cross-reference recorded addresses against the most recently published official directory at time of collection.

**Primary Sources:** NMC, Maharashtra Police, Maharashtra Fire Services official directories.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** A relocated office recorded at its old address would misassign it to the wrong locality.

**Mitigation:** Collection date is recorded for every facility record (VISION.md Section 4), enabling future re-verification against the most current directory.

---

### 8. Real Estate Assumptions

#### A-038 — Infrastructure Accessibility Influences Housing Demand

**Category:** Real Estate

**Statement:** Proximity to schools, hospitals, metro stations, and markets is a meaningful factor in residential demand for a given Nagpur locality.

**Rationale:** This underlies the entire premise that infrastructure-based KPIs (IDI, HAS, MII) are relevant to home buyers and investors, as stated in VISION.md Section 9.

**Evidence Required:** General real estate research literature on accessibility-demand relationships, applied cautiously to the Nagpur context.

**Validation Method:** Where locality-level price or demand proxy data becomes available (e.g., aggregate registration values, per A-019), test for a directional relationship with infrastructure scores; document correlation strength rather than assuming causation.

**Primary Sources:** Academic real estate/urban economics literature (methodological reference only, per A-007); local registration data where available.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** If accessibility turns out to be a weak demand driver in Nagpur specifically (versus other factors like proximity to employment centers), infrastructure-based KPIs would have less real estate relevance than claimed.

**Mitigation:** Claims linking KPIs to real estate demand are framed as documented indicators, not guaranteed predictors, and any correlation testing results are published alongside the methodology.

---

#### A-039 — Infrastructure Density Correlates With, but Does Not Fully Determine, Property Prices

**Category:** Real Estate

**Statement:** IDI and related infrastructure KPIs are expected to show some positive association with property values, but are not assumed to be the sole or dominant price determinant.

**Rationale:** Prevents the project from overstating what its KPIs can predict, consistent with VISION.md Section 7.7's evidence-before-claims standard.

**Evidence Required:** Locality-level price proxy data (where obtainable) to test against infrastructure scores.

**Validation Method:** Statistical correlation testing once both datasets exist; results (whatever they show) are published, not selectively reported.

**Primary Sources:** IGR Maharashtra aggregate registration data, where available.

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** Overstating the infrastructure-price link could mislead an investor relying on the platform.

**Mitigation:** The dashboard and reports explicitly frame KPIs as indicators to inform, not replace, an investor's own due diligence.

---

#### A-040 — Property Registration Values, Where Available, Reflect Genuine Market Trends

**Category:** Real Estate

**Statement:** Where aggregate property registration value data is used, it is assumed to reasonably reflect actual market transaction trends rather than being systematically distorted (e.g., by widespread underreporting of transaction value).

**Rationale:** Registration data is the most accessible public proxy for real estate market activity in India, despite known limitations.

**Evidence Required:** Literature or official commentary on the reliability of Maharashtra registration data as a market proxy.

**Validation Method:** Where possible, compare aggregate registration trends against independent published real estate market reports for Nagpur; document divergence.

**Primary Sources:** IGR Maharashtra, published real estate market reports (for cross-reference only, per A-007's methodology-versus-fact distinction).

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** Known real-world underreporting of transaction values in registration data could distort any price-trend KPI built on it.

**Mitigation:** Any registration-based indicator explicitly states known underreporting risk as a limitation, per VISION.md Section 7.4.

---

#### A-041 — Documented Growth Corridors Attract Disproportionate Investment

**Category:** Real Estate

**Statement:** Localities showing documented infrastructure investment activity (new metro extensions, planned commercial development, government-announced growth corridors) are assumed to attract disproportionate future real estate investment relative to localities without such documented activity.

**Rationale:** This underlies the Growth Signal Index (GSI) concept defined for Version 2.0 in VISION.md Section 4.

**Evidence Required:** Officially announced or documented infrastructure investment plans for specific localities.

**Validation Method:** GSI is built only from documented, sourced investment signals (per VISION.md Section 4's GSI definition) — not informal rumor of "hot" localities.

**Primary Sources:** NMC, Smart City Nagpur Limited announcements; Maha Metro expansion plans.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A documented plan could be delayed or cancelled, meaning a locality flagged as a growth corridor doesn't materialize as expected.

**Mitigation:** GSI documentation explicitly notes that it reflects documented signals and planned activity, not a guarantee of realized growth.

---

#### A-042 — Broker-Reported Prices Cannot Be Independently Verified at Scale

**Category:** Real Estate

**Statement:** Real estate broker-quoted asking prices, as referenced in VISION.md's opening framing, cannot be systematically verified against actual transaction data and are therefore treated as unverifiable market noise rather than a data source for NagpurLens.

**Rationale:** This assumption formalizes exactly what VISION.md's opening line is contrasting NagpurLens against — it is a stated limitation of the broader market, not a gap in NagpurLens's own dataset.

**Evidence Required:** N/A — this documents an intentional exclusion, not a data source to be verified.

**Validation Method:** N/A — enforced by source registry policy: broker-quoted prices are never entered as a registry source category.

**Primary Sources:** Excluded by design.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** N/A.

**Mitigation:** Broker-sourced pricing is permanently out of scope for the source registry; only official registration or published market report data is used for price-related signals.

---

### 9. Demographic Assumptions

#### A-043 — Census 2011 Data Is Internally Reliable Despite Its Age

**Category:** Demographic

**Statement:** While outdated in absolute terms (VISION.md Section 7.4), Census 2011 population figures are assumed to be internally accurate for what they measured at the time, and the relative population distribution across older, established localities has not shifted dramatically.

**Rationale:** Distinguishes "the data is old" (acknowledged) from "the data was wrong even in 2011" (a separate, less likely, claim) — the project only asserts the former.

**Evidence Required:** Census 2011 methodology documentation confirming standard enumeration procedures were followed for Nagpur district.

**Validation Method:** Cross-reference Census 2011 Nagpur totals against Census 2001 for directional consistency (population should show a reasonable, not anomalous, decadal change).

**Primary Sources:** Census of India 2011, Nagpur district handbook.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Rapid post-2011 growth in peripheral localities (VISION.md Section 2) means PIR may understate current infrastructure strain in those specific areas even if the 2011 count itself was accurate at the time.

**Mitigation:** PIR outputs are explicitly labeled with the Census year, and peripheral/rapidly developing localities are flagged for lower confidence in their PIR score specifically.

---

#### A-044 — Ward-to-Locality Population Distribution Can Be Reasonably Approximated

**Category:** Demographic

**Statement:** Where Census data is published at ward level rather than exactly matching NagpurLens's locality boundaries, population can be reasonably approximated to the locality level using area-proportional or boundary-overlap methods.

**Rationale:** Census enumeration units and NagpurLens locality boundaries may not align perfectly one-to-one.

**Evidence Required:** A documented mapping between Census enumeration units and NagpurLens locality boundaries.

**Validation Method:** Boundary overlap analysis, with the approximation method documented in the data dictionary; localities with significant boundary mismatch are flagged with a lower PIR confidence tier.

**Primary Sources:** Census of India ward-level data, NMC boundary maps.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A poor approximation could misstate population for localities that don't align cleanly with Census enumeration units.

**Mitigation:** The approximation method is fully documented and versioned so it can be refined without breaking reproducibility of prior results.

---

#### A-045 — Literacy and Employment Data Availability Is Limited at Locality Level

**Category:** Demographic

**Statement:** Locality-level (rather than district- or ward-level) literacy and employment statistics are only partially available and are not treated as a required input for Version 1 KPIs.

**Rationale:** Sets an honest boundary on what demographic granularity Version 1 actually depends on.

**Evidence Required:** A documented search confirming what locality-level demographic breakdowns are actually published.

**Validation Method:** Source registry audit — literacy/employment data is only incorporated into a KPI if a genuinely locality-level, sourced dataset exists.

**Primary Sources:** Census of India, National Sample Survey Office (NSSO), where locality-level breakdowns exist.

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** N/A — documents a known gap rather than a false claim of availability.

**Mitigation:** Literacy and employment remain outside Version 1 KPI formulas (VISION.md Section 4) unless and until locality-level sourcing is confirmed.

---

#### A-046 — Household Income Data Is Not Available at Locality Level and Is Excluded From Version 1

**Category:** Demographic

**Statement:** No sufficiently granular, publicly available household income dataset exists at the Nagpur locality level; Version 1 does not attempt to estimate or infer income data.

**Rationale:** Avoids introducing an unverified proxy for a sensitive demographic variable, consistent with VISION.md Section 7.7 (evidence before claims).

**Evidence Required:** A documented search confirming the absence of a public, locality-level household income dataset for Nagpur.

**Validation Method:** Source registry audit; income data is added only if a genuinely public, sourced, locality-level dataset is later found.

**Primary Sources:** N/A — excluded by design pending evidence.

**Confidence Level:** High (regarding current unavailability)

**Status:** Pending

**Risk if False:** N/A.

**Mitigation:** No KPI in the current roadmap (VISION.md Section 4) depends on household income; this assumption keeps it that way unless formally revised.

---

#### A-047 — Migration Trends Are a Deferred Input, Not a Version 1 Requirement

**Category:** Demographic

**Statement:** Population movement and migration trends are relevant context for future growth modeling but are not required for any Version 1 KPI, and their absence does not block Version 1 completion.

**Rationale:** VISION.md Section 4 explicitly defers population movement to Version 2's Growth Signal Index once a documented locality-level source exists, rather than treating it as a Version 1 blocker.

**Evidence Required:** Confirmation that Version 1 KPI formulas (IDI, PIR, HAS, MII) contain no migration-dependent term.

**Validation Method:** Formula audit against `kpi_definitions.md` prior to each version release.

**Primary Sources:** N/A — governs scope, not a data source itself.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** N/A.

**Mitigation:** Migration data sourcing becomes an active research task only at the start of Version 2 planning, per VISION.md Section 4's explicit deferral.

---

### 10. Environmental Assumptions

#### A-048 — Green Space Data Availability Is Limited but Partially Usable

**Category:** Environmental

**Statement:** Publicly documented green space and park locations (already included as one of the nine Version 1 infrastructure categories per VISION.md Section 4) are available with reasonable, if incomplete, coverage.

**Rationale:** Parks are already a named infrastructure category, so their data availability directly affects Version 1 completeness.

**Evidence Required:** A cross-reference of NMC-listed parks against independent map sources.

**Validation Method:** Spot-check comparison; completeness gap documented if found.

**Primary Sources:** NMC parks and gardens department, OpenStreetMap.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Under-documented green space could understate a locality's genuine livability profile.

**Mitigation:** Known gaps are stated in `validation_report.md`, consistent with VISION.md Section 7.4.

---

#### A-049 — Pollution Data Is Not Available at Sufficient Locality-Level Granularity for Version 1

**Category:** Environmental

**Statement:** Air and water quality monitoring data for Nagpur exists at station or city level, not at a granularity that supports locality-specific pollution scoring in Version 1.

**Rationale:** Prevents the project from asserting a locality-level pollution score that is actually a citywide average mislabeled as local.

**Evidence Required:** A documented search of Central/State Pollution Control Board monitoring station locations and coverage in Nagpur.

**Validation Method:** Source registry audit confirming monitoring station density and coverage before any pollution-related KPI is considered.

**Primary Sources:** Maharashtra Pollution Control Board, Central Pollution Control Board.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** N/A — documents a scope limitation rather than a false claim.

**Mitigation:** Pollution is excluded from Version 1–3 KPI formulas (VISION.md Section 4) unless locality-level monitoring coverage is later confirmed sufficient.

---

#### A-050 — Flood Risk Data, Where Available, Reflects Documented Historical Events

**Category:** Environmental

**Statement:** Any flood risk information referenced for a locality (e.g., from NMC disaster management records) reflects documented historical flooding events or officially designated flood-prone zones, not informal anecdotal reports.

**Rationale:** Keeps environmental risk claims evidence-based, consistent with VISION.md Section 7.7.

**Evidence Required:** Official disaster management or municipal records naming specific flood-affected areas and dates.

**Validation Method:** Source registry check confirming any flood-related claim traces to an official record.

**Primary Sources:** NMC Disaster Management Cell, state disaster management authority records.

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** Anecdotal flood reputation without official backing could unfairly stigmatize a locality.

**Mitigation:** Flood risk is not included as a scored KPI input unless and until an official, sourced record supports it; currently treated as contextual documentation only, if used at all.

---

#### A-051 — Climate Data Is Out of Scope for Version 1 Through Version 3

**Category:** Environmental

**Statement:** City- or regional-scale climate data (temperature trends, rainfall patterns) is not required by any KPI defined in VISION.md Section 4 and is explicitly out of scope through Version 3.

**Rationale:** Keeps environmental scope bounded to what the roadmap actually requires, preventing scope creep per VISION.md's closing standard (Section 13).

**Evidence Required:** Confirmation that no KPI formula through v3.0 references climate variables.

**Validation Method:** Formula audit against `kpi_definitions.md`.

**Primary Sources:** N/A — governs scope.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** N/A.

**Mitigation:** Climate data sourcing is not pursued unless a future version formally introduces a climate-dependent KPI, revising this document accordingly.

---

#### A-052 — Water Availability Data Is Limited to What Utility Sources Publish

**Category:** Environmental

**Statement:** Locality-level water supply adequacy data is constrained by the same public availability limits described for utility infrastructure in A-035, and Version 1 does not assume comprehensive coverage.

**Rationale:** Avoids duplicating an optimistic availability assumption already flagged as limited under Infrastructure Assumptions.

**Evidence Required:** Same evidence base as A-035.

**Validation Method:** Shared validation process with A-035; water availability is only scored if a genuinely public, sourced dataset is found.

**Primary Sources:** NMC water supply department, Maharashtra Jeevan Pradhikaran.

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** N/A — documents a known gap.

**Mitigation:** Water availability remains outside core Version 1 KPI formulas unless sourcing improves.

---

### 11. AI & Analytics Assumptions

#### A-053 — AI Assists Analysis but Never Replaces Evidence

**Category:** AI & Analytics

**Statement:** Any AI or machine-learning tool used in the NagpurLens pipeline (e.g., for pattern detection, drafting, or anomaly flagging) produces suggestions for human review, never a final published fact or score without independent evidentiary backing.

**Rationale:** Reinforces A-009 specifically within the analytics stage of the pipeline, where model outputs could otherwise be mistaken for verified findings.

**Evidence Required:** Documentation distinguishing AI-suggested findings from evidence-backed, published findings in any analysis workflow.

**Validation Method:** Peer review step (VISION.md Section 6) explicitly checks that any AI-assisted analytical output is traceable to underlying verified data before publication.

**Primary Sources:** N/A — governs process.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** An AI-hallucinated pattern could be mistaken for a genuine finding about Nagpur.

**Mitigation:** All published findings require a data-based reproduction path independent of any AI tool used during drafting or exploration.

---

#### A-054 — All Scoring Models Require Validation Before Publication

**Category:** AI & Analytics

**Statement:** Every KPI formula (IDI, PIR, HAS, MII, GSI, IAS) is tested against the underlying validated dataset and reviewed before being marked "live," per the version-gating explicitly defined in VISION.md Section 4.

**Rationale:** Prevents an unvalidated formula from being presented as an authoritative score.

**Evidence Required:** A completed validation checklist for each KPI prior to its version release.

**Validation Method:** KPI-specific test cases (e.g., known-input/expected-output checks) run in the CI suite before a KPI is tagged "live."

**Primary Sources:** `kpi_definitions.md`, `validation_report.md`.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** An unvalidated formula could ship with a calculation error that skews every locality's ranking.

**Mitigation:** A KPI is only marked "live" after passing its documented validation checklist, exactly as VISION.md Section 4 already specifies.

---

#### A-055 — Every Score Is Explainable and Its Formula Is Public

**Category:** AI & Analytics

**Statement:** Every published KPI, including the composite Investment Attractiveness Score, can be decomposed into its exact inputs and weights, with no hidden or proprietary component.

**Rationale:** Directly required by VISION.md Section 3 ("One number, fully explained, every weight documented, every formula public") and Section 5 (contrasting "black-box scores" with NagpurLens's approach).

**Evidence Required:** A published formula and weighting scheme in `kpi_definitions.md` for every KPI.

**Validation Method:** Documentation review confirming every KPI has a corresponding public formula entry before release.

**Primary Sources:** `kpi_definitions.md`.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** A KPI presented without its formula would directly contradict the project's stated differentiator.

**Mitigation:** No KPI is published to the dashboard without a corresponding public formula document already merged.

---

#### A-056 — Analytical Outputs Are Fully Reproducible From Published Data and Code

**Category:** AI & Analytics

**Statement:** Any ranked list, chart, or score shown on the dashboard can be regenerated exactly from the published dataset and open-source analytics code, without needing undocumented manual adjustment.

**Rationale:** Extends the reproducibility principle (VISION.md Section 7.3) specifically to the analytics and scoring layer, not just the raw data layer.

**Evidence Required:** A test that regenerates each dashboard output from source code and compares it to the published version.

**Validation Method:** Automated regression test comparing freshly computed KPI outputs to previously published ones after any pipeline change.

**Primary Sources:** N/A — internal engineering validation.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A manual, undocumented adjustment to a published score would break the reproducibility guarantee and could introduce unaccountable bias.

**Mitigation:** Any manual correction to a computed value is documented as an explicit, sourced override in the dataset itself, never applied silently downstream.

---

#### A-057 — Feature Engineering Steps Remain Transparent and Documented

**Category:** AI & Analytics

**Statement:** Every derived feature used in a KPI calculation (e.g., "nearest metro station," "infrastructure count per category") is documented with its exact derivation logic, not left as an unexplained intermediate step.

**Rationale:** Undocumented feature engineering is a common source of hidden bias in analytics projects; this assumption closes that gap for NagpurLens.

**Evidence Required:** A documented derivation description for every intermediate feature used in any KPI formula.

**Validation Method:** Documentation completeness review as part of the peer review stage before a KPI is marked "live."

**Primary Sources:** `kpi_definitions.md`, data dictionary.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** An undocumented derived feature could quietly encode an assumption never surfaced for review.

**Mitigation:** No derived feature is used in a published KPI without an accompanying documentation entry.

---

### 12. User Assumptions

#### A-058 — Home Buyers Primarily Need Locality-Level Verified Infrastructure Data

**Category:** User

**Statement:** The Home Buyer persona (VISION.md Section 9) is primarily seeking to verify claims about a specific locality's infrastructure before a purchase decision, rather than city-wide comparative analytics.

**Rationale:** Shapes which dashboard views and KPIs are prioritized for this user group.

**Evidence Required:** The specific need statement already defined in VISION.md Section 9 ("Is this locality actually as well-served as the broker claims?").

**Validation Method:** User feedback (once the dashboard is public) comparing actual usage patterns against this assumed primary need.

**Primary Sources:** VISION.md Section 9 (internal project definition).

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** If home buyers actually want comparative rankings more than locality-specific verification, the dashboard's information architecture may need rebalancing.

**Mitigation:** The dashboard supports both a single-locality lookup view and a comparative view, so the assumption's accuracy affects prioritization, not feasibility.

---

#### A-059 — Investors Need Forward-Looking Growth Signals, Not Just Current-State Data

**Category:** User

**Statement:** The Property Investor persona (VISION.md Section 9) is specifically seeking indicators of future appreciation potential, which is why Growth Signal Index (Version 2) and Investment Attractiveness Score (Version 3) exist as distinct, later-stage products rather than being folded into Version 1.

**Rationale:** Matches VISION.md Section 9's stated investor need directly.

**Evidence Required:** VISION.md Section 9's stated need ("Which localities are 3–5 years ahead of price appreciation?").

**Validation Method:** Post-launch user feedback on whether Version 1's infrastructure-only view is perceived as insufficient by this persona (expected, and why GSI/IAS exist as later versions).

**Primary Sources:** VISION.md Section 9 (internal project definition).

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** N/A — this assumption already anticipates and is consistent with the version-gated roadmap.

**Mitigation:** Version 1 documentation sets investor expectations clearly: full growth and investment scoring arrive in Versions 2–3.

---

#### A-060 — Government and Civic Bodies Need Auditable, Not Just Visual, Data

**Category:** User

**Statement:** The Government/Civic Body persona (VISION.md Section 9) requires data with a documented, auditable methodology suitable for informing budget prioritization decisions — not merely an attractive dashboard.

**Rationale:** Public-sector adoption requires a standard of rigor beyond what a general consumer dashboard needs, matching VISION.md Section 7.1 and 7.3.

**Evidence Required:** A complete methodology and validation report accompanying any data shared with a civic body.

**Validation Method:** Direct feedback from any engagement with NMC or Smart City Nagpur Limited on documentation adequacy.

**Primary Sources:** VISION.md Section 9 (internal project definition); direct civic body feedback, if and when engagement occurs.

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** A civic body may require a level of formal certification or legal review beyond what an open-source student project can currently provide.

**Mitigation:** Documentation is built to the highest achievable rigor from the outset, so it is ready if and when formal civic engagement occurs, per VISION.md Section 11's mention of exploring civic deployments.

---

#### A-061 — Researchers Need Reproducible, Source-Documented Data Above All Else

**Category:** User

**Statement:** The Researcher/Urban Planner persona (VISION.md Section 9) values methodological rigor and reproducibility over visual polish or breadth of features.

**Rationale:** Matches VISION.md Section 9's stated need directly ("Is there a research-grade urban dataset for Nagpur I can build on?").

**Evidence Required:** VISION.md Section 9's stated need statement.

**Validation Method:** Feedback from any academic or research user who adopts the dataset for their own work.

**Primary Sources:** VISION.md Section 9 (internal project definition).

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** N/A — low downside, since rigor also serves every other user persona.

**Mitigation:** Documentation and methodology rigor is treated as a baseline requirement for all personas, not a researcher-specific add-on.

---

#### A-062 — General Dashboard Users Can Interpret KPI Outputs Given Clear Definitions

**Category:** User

**Statement:** A non-technical dashboard visitor can correctly interpret a KPI score (e.g., "IDI: 4.2") if it is accompanied by a clear, plain-language definition and context, without needing to understand the underlying formula.

**Rationale:** The dashboard must serve home buyers and investors (VISION.md Section 9) who are not expected to be data scientists, while still keeping every formula public (VISION.md Section 3) for those who want the detail.

**Evidence Required:** Usability review of KPI definition text presented alongside each score in the dashboard.

**Validation Method:** Informal usability testing with representative non-technical users once the dashboard is public.

**Primary Sources:** N/A — internal UX validation.

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** Users could misinterpret a raw KPI number without adequate context, undermining the platform's decision-support goal.

**Mitigation:** Every dashboard KPI display includes an accessible plain-language explanation alongside the number and a link to the full public formula.

---

### 13. Dashboard Assumptions

#### A-063 — Streamlit Is Sufficient for Version 1 Visualization Requirements

**Category:** Dashboard

**Statement:** Streamlit, as named in the pipeline (VISION.md Section 6), can adequately support the interactive filtering, mapping, and KPI display needs of Version 1 without requiring a custom front-end framework.

**Rationale:** Keeps the engineering pipeline achievable for the current team size while meeting VISION.md Section 10's requirement that "the Streamlit dashboard is live and publicly accessible."

**Evidence Required:** A working prototype demonstrating the required interactive features (locality selection, KPI display, basic mapping).

**Validation Method:** Build and review a working prototype against the feature list required for Version 1's dashboard success metric.

**Primary Sources:** N/A — internal tooling decision, already fixed in VISION.md Section 6.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Streamlit's performance or customization limits could become a bottleneck as data volume grows in later versions.

**Mitigation:** VISION.md Section 6 already scopes Streamlit specifically to the dashboard stage, with FastAPI as the underlying API layer — a future front-end swap would not require rebuilding the API or data layers.

---

#### A-064 — Locality-Level Filtering Meets Version 1 User Needs

**Category:** Dashboard

**Statement:** Filtering the dashboard by individual locality (and by infrastructure category) is sufficient interactivity for Version 1's target users, without requiring more advanced filtering (e.g., multi-variable custom queries) at this stage.

**Rationale:** Matches the scope of Version 1's headline question (VISION.md Section 8: "Where does infra lag vs population") which is fundamentally a locality-comparison question.

**Evidence Required:** Confirmation that the Version 1 success metrics (VISION.md Section 10) do not require more advanced filtering.

**Validation Method:** Review dashboard feature list against Version 1 success metrics before release.

**Primary Sources:** N/A — internal scope validation against VISION.md Section 10.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Users wanting to filter across multiple criteria simultaneously (e.g., "localities with high IDI and low PIR") may find Version 1 limiting.

**Mitigation:** The FastAPI layer (VISION.md Section 6) is built to support more complex queries even if the Version 1 Streamlit UI only exposes basic filters, allowing future UI enhancement without new API work.

---

#### A-065 — Dashboard Data Freshness Expectations Are Explicitly Documented, Not Real-Time

**Category:** Dashboard

**Statement:** Users understand, via clear documentation, that dashboard data reflects the date of last verified collection (e.g., Census 2011, or the most recent infrastructure survey date) rather than real-time civic conditions.

**Rationale:** Prevents users from mistaking a periodically-updated research dataset for a live civic feed, consistent with VISION.md Section 7.4's transparency principle.

**Evidence Required:** A visible "last updated" or "data as of" date on every dashboard view.

**Validation Method:** UI review confirming every KPI and data table displays its collection or verification date.

**Primary Sources:** N/A — internal UX requirement.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** A user could make a decision based on data they mistakenly believe is current.

**Mitigation:** Data freshness labeling is a mandatory element of every dashboard component, not an optional footnote.

---

#### A-066 — Dashboard Performance Is Acceptable at Version 1 Data Scale

**Category:** Dashboard

**Statement:** With roughly 50+ localities and 150+ infrastructure records (VISION.md Section 10), Streamlit and the underlying MySQL/FastAPI stack can serve dashboard queries with response times acceptable for public use, without requiring specialized performance optimization at this stage.

**Rationale:** Version 1's dataset size (VISION.md Section 10) is modest enough that this is a reasonable engineering assumption rather than requiring premature optimization.

**Evidence Required:** Load-testing results at the expected Version 1 data scale.

**Validation Method:** Basic performance testing (query response time under typical filtering operations) before public launch.

**Primary Sources:** N/A — internal engineering validation.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** Poor performance at launch could undermine public credibility even if the underlying data is sound.

**Mitigation:** Performance testing is included in the pre-launch checklist; optimization is revisited if Version 2/3 data volume grows significantly.

---

#### A-067 — Basic Accessibility Standards Are Achievable Within Version 1 Scope

**Category:** Dashboard

**Statement:** Common web accessibility practices (readable color contrast, keyboard navigability where feasible within Streamlit's constraints, clear text alternatives for charts) can be implemented within Version 1's engineering timeline without requiring a dedicated accessibility specialist.

**Rationale:** A public civic data platform (per VISION.md Section 9's inclusion of government and general citizen-adjacent users) should not exclude users with accessibility needs, within reasonable effort for the team's current resources.

**Evidence Required:** A basic accessibility checklist applied to the dashboard before public launch.

**Validation Method:** Manual accessibility review (contrast checks, alt-text presence) prior to release.

**Primary Sources:** N/A — internal QA practice; general web accessibility guidelines used as a methodology reference (per the A-007 distinction).

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** Streamlit's component library may impose accessibility limitations the team cannot fully work around.

**Mitigation:** Known accessibility limitations of the chosen tooling are documented honestly rather than claimed as fully solved, consistent with VISION.md Section 7.4.

---

### 14. Operational Assumptions

#### A-068 — The Repository Remains Actively Maintained by the Core Team

**Category:** Operational

**Statement:** Mohammad Ammar, Zahid Khan, and Mohammad Ruwaifa (VISION.md Section 12) will continue actively maintaining the repository through at least the Version 1 milestone defined in VISION.md Section 8.

**Rationale:** Reproducibility and documentation currency (VISION.md Section 7.3) depend on ongoing maintenance, not a one-time publish.

**Evidence Required:** Commit history and issue-response activity over time.

**Validation Method:** Periodic review of repository activity against the roadmap timeline.

**Primary Sources:** N/A — internal team commitment, as documented in VISION.md Section 12.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** An abandoned repository would freeze the dataset at whatever state it reached, undermining ongoing trust.

**Mitigation:** Version milestones (VISION.md Section 8) are used as clear checkpoints to assess project momentum and, if needed, adjust scope or bring in additional contributors.

---

#### A-069 — GitHub Actions CI Reliably Enforces Version Control Discipline

**Category:** Operational

**Statement:** The GitHub Actions CI pipeline (VISION.md Section 5, 10) will reliably run validation tests on every push and reliably block merges that fail those tests.

**Rationale:** This is the mechanism that operationalizes "all unit tests pass on every push" (VISION.md Section 10) as an enforced standard rather than a manual best-effort practice.

**Evidence Required:** CI configuration and a record of blocked/passed pipeline runs.

**Validation Method:** Periodic audit confirming CI is active and correctly gating merges (e.g., attempting a deliberately failing test case in a branch to confirm the pipeline blocks it).

**Primary Sources:** N/A — internal DevOps configuration.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** A misconfigured or bypassed CI pipeline could let unvalidated data or code reach the main branch.

**Mitigation:** Branch protection rules require CI to pass before merge, and this configuration is periodically re-verified.

---

#### A-070 — Documentation Is Kept in Sync With Code and Data Changes

**Category:** Operational

**Statement:** Every material change to the schema, KPI formulas, or dataset is accompanied by a corresponding documentation update in the same pull request, not a deferred "update docs later" task.

**Rationale:** Documentation drift would undermine the reproducibility standard (VISION.md Section 7.3) even if the underlying code and data remain correct.

**Evidence Required:** Pull request history showing documentation changes alongside corresponding code/data changes.

**Validation Method:** Pull request review checklist requiring a documentation diff for any schema, formula, or dataset change.

**Primary Sources:** N/A — internal contribution standard.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A developer reproducing the project from documentation alone could hit a mismatch between documented and actual schema/formulas.

**Mitigation:** Peer review (VISION.md Section 6) explicitly includes a documentation-sync check before merge.

---

#### A-071 — Issue Tracking Captures Data Corrections and Disputes

**Category:** Operational

**Statement:** Any reported error, disputed data point, or suggested correction from a contributor or user is logged as a tracked GitHub issue, creating an auditable record of how and why a dataset value changed.

**Rationale:** Supports the auditability principle (VISION.md Section 7.3) by ensuring data corrections themselves are traceable, not made as silent, undocumented edits.

**Evidence Required:** A documented issue-tracking workflow for data corrections specifically (as distinct from general bug/feature issues).

**Validation Method:** Review of issue tracker history for a sample of past data corrections to confirm they are properly logged.

**Primary Sources:** N/A — internal process governance.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** An undocumented silent correction would break the audit trail the project depends on for credibility.

**Mitigation:** Any correction to a published data point requires an associated issue reference in its commit message.

---

#### A-072 — Future Contributors Can Onboard Using Documentation Alone

**Category:** Operational

**Statement:** A new contributor, with no prior verbal briefing from the core team, can understand the project's scope, standards, and pipeline well enough to contribute correctly, using only the repository's documentation.

**Rationale:** Directly required by VISION.md Section 7.3's reproducibility standard, extended to the contribution process itself, not just data reproduction.

**Evidence Required:** A complete `CONTRIBUTING.md` or equivalent covering standards referenced throughout this document (source citation, validation, KPI documentation).

**Validation Method:** A "cold onboarding" test — inviting a contributor unfamiliar with the project to attempt a small documented task using only the repository's written guidance.

**Primary Sources:** N/A — internal documentation completeness check.

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** Undocumented tribal knowledge could create a bottleneck dependent on the three founding contributors.

**Mitigation:** Contribution standards are written down and iteratively improved based on onboarding friction observed in practice.

---

### 15. Legal & Ethical Assumptions

#### A-073 — Aggregate Locality Data Does Not Compromise Individual Privacy

**Category:** Legal & Ethical

**Statement:** Because NagpurLens operates exclusively at the locality/aggregate level (population counts, facility counts) and never at the individual or household level, no individual's personal information is collected, stored, or exposed by the platform.

**Rationale:** Sets a clear privacy boundary for the entire dataset design, distinguishing NagpurLens from platforms that handle individual transaction or personal records.

**Evidence Required:** Schema review confirming no field captures individual-level personal data.

**Validation Method:** Data dictionary audit confirming every table operates at locality or facility level, never individual/household level.

**Primary Sources:** N/A — internal schema and privacy design review.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** Inadvertent inclusion of individual-level data (e.g., a facility record containing a named contact person's personal phone number) could create a privacy exposure.

**Mitigation:** Any incidentally collected individual-level detail (e.g., contact fields in a scraped facility directory) is stripped during ingestion and not stored in the production schema.

---

#### A-074 — Reused Datasets Carry Licenses Compatible With Open Redistribution

**Category:** Legal & Ethical

**Statement:** Every third-party dataset incorporated into NagpurLens carries a license that permits open, attributed redistribution of the data itself or of derived KPIs computed from it.

**Rationale:** Directly extends A-014 into a legal/ethical governance commitment, since NagpurLens is presented as an open-source platform.

**Evidence Required:** A recorded license type for every source in `source_registry.csv`.

**Validation Method:** License compatibility audit before any dataset or derived KPI is published in the open-source repository.

**Primary Sources:** Government open data licenses; official terms of use for each registered source.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** Publishing a restrictively licensed dataset in an open repository could create legal exposure.

**Mitigation:** Any source with an unclear or restrictive license is used only for internal validation reference, never redistributed in raw or minimally-transformed form.

---

#### A-075 — Government Data Usage Complies With Each Source's Stated Terms

**Category:** Legal & Ethical

**Statement:** NagpurLens's use of government-published datasets (citation, aggregation, redistribution of derived KPIs) falls within the terms of use published by each respective government body.

**Rationale:** Ensures the project's core data foundation is used lawfully and respectfully of the publishing authority's stated conditions.

**Evidence Required:** The specific terms-of-use text for each government source used.

**Validation Method:** Terms-of-use review recorded alongside each government source in the registry.

**Primary Sources:** NMC, Census of India, Government of Maharashtra official terms of use pages.

**Confidence Level:** Medium

**Status:** Pending

**Risk if False:** A government source could restrict commercial or redistributive use in ways not initially noticed.

**Mitigation:** Where terms of use are ambiguous or unpublished, the project defaults to citation-only, non-redistributive use of the raw source until clarity is obtained.

---

#### A-076 — Responsible AI Use Excludes Automated Decisions About Individuals

**Category:** Legal & Ethical

**Statement:** NagpurLens's KPIs and any AI-assisted analytics inform decisions made by individual humans (buyers, investors, planners) about localities; they are never used to make automated decisions about specific individual people.

**Rationale:** Draws a clear ethical boundary given the project's locality-level scope and the general responsible-AI principle that automated scoring should not be applied to individuals without their knowledge or consent.

**Evidence Required:** Confirmation that no KPI or feature in the roadmap (VISION.md Sections 4, 8) operates at the individual-person level.

**Validation Method:** Scope review against `kpi_definitions.md` confirming all KPIs remain locality-level.

**Primary Sources:** N/A — internal ethical scope boundary.

**Confidence Level:** High

**Status:** Pending

**Risk if False:** N/A — this is a boundary the project sets for itself, not a claim about external reality.

**Mitigation:** Any future feature proposal that would score or profile individual people is explicitly out of scope and would require a full, separate ethical review before consideration.

---

#### A-077 — Locality Scoring Methodology Is Actively Reviewed for Systemic Bias

**Category:** Legal & Ethical

**Statement:** KPI weighting and formula design (particularly for the composite Investment Attractiveness Score in Version 3) are periodically reviewed to check whether they systematically disadvantage lower-income or historically underserved localities in ways not justified by the underlying evidence.

**Rationale:** A composite score with undocumented or poorly considered weighting could inadvertently encode existing inequities as if they were neutral facts, contradicting VISION.md Section 7.1's trust standard.

**Evidence Required:** A documented bias-review process applied before any weighting scheme is finalized, especially for IAS.

**Validation Method:** Compare KPI outcomes across historically underserved versus well-served localities to check the scoring reflects genuine, sourced infrastructure gaps rather than compounding an unrelated bias in the underlying source data itself.

**Primary Sources:** N/A — internal methodology governance process, applied to `kpi_definitions.md`.

**Confidence Level:** Low

**Status:** Pending

**Risk if False:** An IAS formula could unintentionally penalize localities already underserved by the very infrastructure gap NagpurLens exists to surface, rather than fairly flagging them for investment attention.

**Mitigation:** VISION.md Section 4 already states IAS weighting "may evolve based on validation, expert review, and empirical evaluation" with every revision documented — this assumption formalizes bias review as part of that expert review process.

---

## Assumption Register

| ID | Category | Summary | Confidence | Status |
|---|---|---|---|---|
| A-001 | Project | Scope follows VISION.md's Nagpur/NMC geography, terminology unresolved but not narrowed | High | Pending |
| A-002 | Project | Sufficient evidence exists to answer the 3 core questions | Medium | Pending |
| A-003 | Project | Data quality prioritized over dataset size | High | Pending |
| A-004 | Project | Full pipeline is reproducible from repo alone | Medium | Pending |
| A-005 | Project | Version sequencing reflects real technical dependency | High | Pending |
| A-006 | Research | Government publications are the authoritative baseline | High | Pending |
| A-007 | Research | Academic literature guides methodology, not facts | High | Pending |
| A-008 | Research | Every public claim requires a named citation | High | Pending |
| A-009 | Research | AI cannot substitute for primary evidence | High | Pending |
| A-010 | Research | Multiple independent sources raise confidence | High | Pending |
| A-011 | Source | Registered sources remain publicly accessible | Medium | Pending |
| A-012 | Source | Source registry is complete and authoritative | High | Pending |
| A-013 | Source | Official portals publish authentic data | High | Pending |
| A-014 | Source | Dataset licenses permit research/redistribution use | Medium | Pending |
| A-015 | Source | Archived datasets remain available long-term | Medium | Pending |
| A-016 | Data Collection | Government datasets reasonably represent reality | Medium | Pending |
| A-017 | Data Collection | Published infrastructure coordinates are correctly geocoded | Medium | Pending |
| A-018 | Data Collection | Ward/locality boundaries stable within a version cycle | Medium | Pending |
| A-019 | Data Collection | Aggregate registration records are usable | Low | Pending |
| A-020 | Data Collection | Census 2011 is the best available population baseline | High/Low | Pending |
| A-021 | Data Collection | Up to 6-decimal coordinates where available; precision follows source, not a fixed floor | High | Pending |
| A-022 | Data Engineering | Locality ID standard is a reliable join key | High | Pending |
| A-023 | Data Engineering | CRS can be standardized across sources | High | Pending |
| A-024 | Data Engineering | Missing values can be reliably identified | High | Pending |
| A-025 | Data Engineering | Duplicate records can be reliably detected | Medium | Pending |
| A-026 | Data Engineering | Schema evolution won't break reproducibility | Medium | Pending |
| A-027 | Geospatial | All coordinates use standard WGS84 | High | Pending |
| A-028 | Geospatial | Haversine nearest-centroid joins are reliable | Medium | Pending |
| A-029 | Geospatial | Straight-line distance is a meaningful accessibility proxy | Medium | Pending |
| A-030 | Geospatial | Boundary definitions used are internally consistent | Medium | Pending |
| A-031 | Geospatial | Coordinates fall within Nagpur district bounds | High | Pending |
| A-032 | Infrastructure | Recorded metro stations are operational | High | Pending |
| A-033 | Infrastructure | Road/highway data reflects current layout | Medium | Pending |
| A-034 | Infrastructure | School/hospital directories are reasonably complete | Medium | Pending |
| A-035 | Infrastructure | Utility data availability is limited | Low | Pending |
| A-036 | Infrastructure | Zoning is optional contextual metadata, recorded only if already published | Medium | Pending |
| A-037 | Infrastructure | Government office locations are relatively permanent | High | Pending |
| A-038 | Real Estate | Accessibility influences housing demand | Medium | Pending |
| A-039 | Real Estate | Infrastructure density correlates with, not determines, price | Low | Pending |
| A-040 | Real Estate | Registration values reflect genuine market trends | Low | Pending |
| A-041 | Real Estate | Documented growth corridors attract investment | Medium | Pending |
| A-042 | Real Estate | Broker-reported prices are unverifiable, excluded | High | Pending |
| A-043 | Demographic | Census 2011 is internally reliable despite its age | Medium | Pending |
| A-044 | Demographic | Ward-to-locality population approximation is reasonable | Medium | Pending |
| A-045 | Demographic | Literacy/employment data limited at locality level | Low | Pending |
| A-046 | Demographic | Household income data unavailable, excluded from v1 | High | Pending |
| A-047 | Demographic | Migration trends deferred to Version 2 | High | Pending |
| A-048 | Environmental | Green space data partially available | Medium | Pending |
| A-049 | Environmental | Pollution data lacks locality-level granularity | Medium | Pending |
| A-050 | Environmental | Flood risk data reflects documented events only | Low | Pending |
| A-051 | Environmental | Climate data out of scope through v3.0 | High | Pending |
| A-052 | Environmental | Water availability data limited, same as utilities | Low | Pending |
| A-053 | AI & Analytics | AI assists but never replaces evidence | High | Pending |
| A-054 | AI & Analytics | All scoring models are validated before publication | High | Pending |
| A-055 | AI & Analytics | Every score is explainable, formula public | High | Pending |
| A-056 | AI & Analytics | Analytical outputs are fully reproducible | Medium | Pending |
| A-057 | AI & Analytics | Feature engineering remains transparent | High | Pending |
| A-058 | User | Home buyers need locality-level verification | Medium | Pending |
| A-059 | User | Investors need forward-looking growth signals | Medium | Pending |
| A-060 | User | Civic bodies need auditable, not just visual, data | Low | Pending |
| A-061 | User | Researchers prioritize reproducibility over polish | Medium | Pending |
| A-062 | User | General users can interpret KPIs given clear definitions | Low | Pending |
| A-063 | Dashboard | Streamlit suffices for v1 visualization needs | Medium | Pending |
| A-064 | Dashboard | Locality-level filtering meets v1 needs | Medium | Pending |
| A-065 | Dashboard | Data freshness is explicitly documented, not real-time | High | Pending |
| A-066 | Dashboard | Performance is acceptable at v1 data scale | High | Pending |
| A-067 | Dashboard | Basic accessibility standards are achievable | Low | Pending |
| A-068 | Operational | Core team remains actively maintaining the repo | Medium | Pending |
| A-069 | Operational | CI reliably enforces validation discipline | High | Pending |
| A-070 | Operational | Documentation stays in sync with code/data | Medium | Pending |
| A-071 | Operational | Issue tracking captures data corrections | Medium | Pending |
| A-072 | Operational | Future contributors can onboard from docs alone | Low | Pending |
| A-073 | Legal & Ethical | Aggregate data does not compromise individual privacy | High | Pending |
| A-074 | Legal & Ethical | Reused datasets carry redistribution-compatible licenses | Medium | Pending |
| A-075 | Legal & Ethical | Government data usage complies with stated terms | Medium | Pending |
| A-076 | Legal & Ethical | Responsible AI excludes automated decisions on individuals | High | Pending |
| A-077 | Legal & Ethical | Locality scoring is reviewed for systemic bias | Low | Pending |

---

## Validation Matrix

| Assumption | Evidence Needed | Validation Technique | Success Criteria |
|---|---|---|---|
| A-001 | NMC ward/boundary map | Cross-check every locality against boundary list | 100% of listed localities confirmed within NMC jurisdiction |
| A-004 | Clean-environment pipeline run | Fresh-clone reproduction test | Output matches published dataset exactly |
| A-010 | Independent geographic references per locality | Coordinate agreement check across sources | Agreement within documented tolerance for 100% of localities |
| A-012 | Table-to-registry field mapping | Automated `source_id` presence check | Zero unregistered data points |
| A-017 | Coordinate precision metadata | GPS bounds + precision test | Zero out-of-bounds coordinates; precision documented per record |
| A-020 | Search for newer locality-level population data | Documented source search, repeated per milestone | Confirmed absence, or newer source identified and integrated |
| A-022 | Join integrity across tables | Automated foreign-key/orphan test in CI | Zero orphan `locality_id` values |
| A-025 | Proximity/name-similarity duplicate scan | Automated duplicate-detection pass + manual review | All flagged duplicates resolved before publish |
| A-028 | Manually verified locality assignment sample | Spot-check comparison against automated Haversine result | Discrepancy rate documented; below defined acceptable threshold |
| A-031 | Nagpur district bounding box | Automated bounds-check test in CI | 100% of coordinate records within bounds |
| A-032 | Maha Metro operational line map | Cross-reference at each version milestone | Zero non-operational stations recorded as operational |
| A-042 | N/A (exclusion policy) | Source registry policy enforcement | Zero broker-quoted prices in registry |
| A-054 | KPI-specific validation checklist | CI test cases per KPI | KPI marked "live" only after 100% checklist pass |
| A-055 | Public formula documentation | Documentation review before release | Every published KPI has a linked public formula entry |
| A-065 | "Data as of" labeling on dashboard | UI review | 100% of dashboard views display a freshness date |
| A-069 | CI configuration and merge logs | Deliberate failing-test branch check; automated push-trigger verification | CI executes automatically on every GitHub push and blocks any push/merge with failing tests (VISION.md Section 5, Section 10) |
| A-008 / A-011 / A-012 / A-013 | Source registry entries: publisher name, public URL/access method, publication/access date, license or domain-authority tag, and `source_id` | Registry field-completeness audit against `source_registry.csv` | 100% of published data points trace to a source that is named, publicly accessible, dated, and registered with a `source_id` (VISION.md Section 5, Section 10) |
| A-008 | Published README content | Documentation review confirming the lead finding is numbered and traceable to the validated dataset | README opens with a real, numbered finding drawn from the validated dataset, per VISION.md Section 5 and Section 10 |
| A-073 | Data dictionary field-level audit | Schema review for individual-level fields | Zero individual-level personal data fields present |
| A-077 | Locality outcome comparison by service level | Bias-pattern review of KPI outputs | No unexplained systemic penalty for underserved localities identified, or documented mitigation applied |

---

## Risk Matrix

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| A government source is removed or restructured before archival | Medium | High | Archive raw source snapshots at ingestion where license permits (A-015) |
| Census 2011 understates population in fast-growing peripheral localities | High | Medium | Label every PIR output with Census year; flag peripheral localities with lower confidence (A-020, A-043) |
| Misgeocoded infrastructure record assigned to wrong locality | Low–Medium | High | Haversine validation + GPS bounds test in CI (A-017, A-028, A-031) |
| Undetected duplicate infrastructure records inflate IDI/HAS | Low–Medium | Medium | Automated duplicate detection with manual review before publish (A-025) |
| KPI formula ships with a calculation error | Low | High | Mandatory validation checklist before any KPI is marked "live" (A-054) |
| Restrictively licensed dataset published in the open repository | Low | High | License audit before publication; unclear-license sources kept internal-only (A-014, A-074) |
| Streamlit performance degrades as data volume grows in v2/v3 | Low (v1) / Medium (v2+) | Medium | Load-test at each version milestone; FastAPI layer decoupled from front-end choice (A-063, A-066) |
| Composite IAS formula (v3.0) unintentionally penalizes underserved localities | Low–Medium | High | Documented bias review as part of formula finalization (A-077) |
| Core team availability decreases, stalling maintenance | Medium | High | Version milestones used as momentum checkpoints; contribution docs kept onboarding-ready (A-068, A-072) |
| Ward/locality boundary redrawn mid-collection cycle | Low | Medium | Locality ID versioning triggered by any confirmed boundary change (A-005, A-018) |

---

## Assumption Governance

**Who updates assumptions.** Any core team member (VISION.md Section 12) may propose a new assumption or a status change to an existing one via pull request. Mohammad Ammar, as Project Lead, has final sign-off on any change to an assumption's `Status` field from `Pending` to `Accepted` or `Rejected`.

**When assumptions are reviewed.** Every assumption is reviewed at three trigger points: (1) before its dependent dataset or KPI is marked "live," (2) at every version milestone defined in VISION.md Section 8, and (3) whenever new contradicting evidence is raised via an issue.

**How rejected assumptions are archived.** A rejected assumption is never deleted from this document. Its `Status` is updated to `Rejected`, a dated note is appended explaining the contradicting evidence, and the assumption entry is moved to an `## Archived Assumptions` appendix (added to this document only once a rejection first occurs) so the historical reasoning remains auditable.

**How new assumptions are added.** A new assumption is added using the standard template, assigned the next sequential ID, placed in the relevant category section, and added to the Assumption Register, Validation Matrix, and Risk Matrix (where applicable) in the same pull request.

**How assumptions affect downstream datasets.** No table, KPI, or dashboard feature may be marked "live" while it depends on an assumption still in `Pending` status without a documented interim confidence caveat. A `Rejected` assumption immediately triggers a flag in `PROBLEM.md` against every downstream artifact that depended on it, pending re-validation.

---

## Maintenance Policy

**Versioning.** This document follows the same version number as the overall NagpurLens release it supports (e.g., a revision made during v1.0 development is tracked as v1.0; a substantial revision during v2.0 development becomes v2.0). Every version bump is accompanied by a dated changelog entry at the top of this file.

**Review frequency.** A full read-through of every assumption's `Status` and `Confidence Level` is performed at minimum once per version milestone (VISION.md Section 8), and additionally whenever a link-check (A-011) or source-registry audit (A-012) is run.

**Ownership.** Mohammad Ammar owns final governance of this document as Project Lead. Mohammad Ruwaifa owns the research- and source-related categories (Sections 2–4, 8–10) given his role in primary research and verification. Zahid Khan owns documentation formatting and consistency across this file and the rest of the repository.

**Documentation standards.** Every assumption must use the fixed template exactly (Category, Statement, Rationale, Evidence Required, Validation Method, Primary Sources, Confidence Level, Status, Risk if False, Mitigation). No field may be left blank; a genuinely not-applicable field states "N/A" with a one-line reason, never silence.

**GitHub pull request expectations.** Any pull request that adds, edits, or changes the status of an assumption must: (1) update the Assumption Register table, (2) update the Validation Matrix and/or Risk Matrix if the assumption's evidence or risk profile changed, and (3) reference the specific VISION.md section the assumption traces back to, if applicable. Pull requests that fail any of these three checks are not merged.

---

## Governance Change Log

| Section | Assumption ID(s) | Change Made | Reason |
|---|---|---|---|
| Document Header | — | Version bumped v1.0 → v1.1; added top-of-document Changelog entry | Maintenance Policy requires a dated changelog entry with every version bump |
| Project Assumptions | A-001 | Restated to acknowledge VISION.md's mixed terminology ("Nagpur," "Nagpur district," "NMC jurisdiction") without resolving it into a boundary stricter than VISION.md itself defines | VISION.md Sections 2 and 4 use these terms interchangeably without a single formal boundary; the prior wording risked a stricter interpretation than the Vision intends |
| Data Collection Assumptions | A-021 | Restated so six-decimal coordinate precision is a ceiling available "where the source supports it," with validation following each source's own precision, not a fixed six-decimal floor | Aligns with VISION.md Section 10's success metric, "coordinates validated to the precision appropriate for the data source," which is intentionally more flexible than Section 4's "6 decimal places" read in isolation |
| Data Engineering Assumptions | A-023, A-024, A-025, A-026 | Added a clarifying sentence to each Rationale identifying the assumption as an internal engineering implementation standard supporting a specific VISION.md requirement, not an independent project requirement | These assumptions are correct and necessary but read as freestanding engineering mandates; reframing makes their subordination to VISION.md explicit |
| Infrastructure Assumptions | A-036 | Restated so commercial/industrial/residential zoning is optional contextual metadata, recorded only when an official source already publishes it, never independently researched, and never an input to any VISION.md Section 4 KPI formula | Zoning classification is not listed in the Version 1 Core Dataset (VISION.md Section 4); the prior wording implied it was a standing data-collection target rather than incidental context |
| Assumption Register | A-001, A-021, A-036 | Updated Summary column to match the revised Statement text | Keeps the register table consistent with the full assumption entries after the above revisions |
| Validation Matrix | A-069 | Broadened Evidence Needed and Success Criteria to explicitly require validation to execute automatically on every GitHub push, not just to block failing merges | VISION.md Section 5 and Section 10 both specify automatic execution on every push, not only merge-blocking behavior |
| Validation Matrix | A-008, A-011, A-012, A-013 | Added a combined Source Validation row requiring every published data point to trace to a source that is named, public, dated, and registered with a `source_id` | VISION.md Section 5 ("every source named and dated") and Section 10 ("every data point has a named, dated, public source") were not previously represented as a single explicit matrix requirement |
| Validation Matrix | A-008 | Added a README Validation row requiring the README to open with a real, numbered finding traceable to the validated dataset | VISION.md Section 5 and Section 10 both list this as an explicit differentiator/success metric; it was not previously represented in the Validation Matrix |

---

*This document is a living governance artifact. Every assumption here is a claim the NagpurLens team is prepared to test — not a belief exempted from testing.*
