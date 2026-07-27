# Sources & Research Standards

**Document ID:** `DOC-RESEARCH-001`
**Repository Path:** `02_research/sources.md`
**Status:** Active — Binding
**Applies To:** All datasets, analyses, models, dashboards, reports, and documentation produced under the NagpurLens project

---

## 1. Purpose

NagpurLens converts fragmented public information about Nagpur into structured, validated, and decision-ready urban intelligence. The value of that intelligence is entirely dependent on the credibility of the data beneath it. A platform that informs citizens, investors, researchers, businesses, planners, and policymakers cannot afford unverifiable numbers.

This document exists to make one requirement explicit and non-negotiable: **every statistic, coordinate, dataset, claim, infrastructure record, investment indicator, demographic value, and research finding produced by this project must be traceable back to a specific, registered, original source.**

This file is the research constitution of the repository. It defines:

- which sources may be used, and which are prohibited
- how sources are ranked, registered, and cited
- how datasets must be documented
- how evidence is stored and validated
- the workflow every contributor must follow when introducing new data

No dataset, script, notebook, SQL table, dashboard panel, or report may enter the repository without complying with the standards below. Where this document conflicts with convenience, this document wins. Where a number cannot be traced to a Source ID, it does not belong in NagpurLens — it belongs in `assumptions.md`, clearly flagged as an assumption, until it is replaced with sourced data.

---

## 2. Research Philosophy

NagpurLens is built on nine principles. These are not aspirational — they are enforced through the workflow, checklists, and validation policy defined later in this document.

| Principle | Meaning in Practice |
|---|---|
| **Accuracy** | A wrong number is worse than a missing number. Missing data is flagged; wrong data corrupts every downstream score. |
| **Transparency** | Every derived metric (IAS, IDI, PIR, GSI, HAS, MII) must show its inputs and their sources, not just its output. |
| **Reproducibility** | A second researcher, given the same Source IDs, must be able to reconstruct the same dataset. |
| **Evidence Over Assumptions** | Claims require citations. Assumptions require explicit labeling in `assumptions.md` and a linked gap-tracking entry. |
| **Official Data First** | Government and municipal records take precedence over any secondary interpretation of the same fact. |
| **Explainability** | If a score cannot be explained to a non-technical stakeholder using its source chain, it is not ready to ship. |
| **Data Provenance** | Every record carries an unbroken chain from raw file to final dashboard value. |
| **Version Control** | Sources, datasets, and derived metrics are versioned; nothing is silently overwritten. |
| **Long-Term Maintainability** | Documentation is written for contributors who join two years from now, not just for the current team. |

---

## 3. Source Hierarchy

Every source is assigned to one of five reliability tiers at the moment it is registered. The tier determines whether the source may be used at all, and if so, under what validation burden.

### Tier A — Primary Official Sources
Government agencies, municipal corporations, official GIS portals, statutory reports, census data, official gazettes.

*Why this tier exists:* These bodies are the legal and administrative authorities over the data they publish (land records, ward boundaries, infrastructure spend, population counts). They carry accountability — publishing incorrect official data has legal and political consequences for them, which is the strongest available reliability signal.

*Examples:* Nagpur Municipal Corporation (NMC), Nagpur Metropolitan Region Development Authority (NMRDA), Census of India, Maharashtra Metro Rail Corporation (Maha Metro), Maharashtra Industrial Development Corporation (MIDC), Multi-modal International Cargo Hub and Airport at Nagpur (MIHAN/MADC).

### Tier B — Academic & Research Institutions
Universities, government-affiliated research institutes, international organizations (World Bank, UN-Habitat), peer-reviewed journals.

*Why this tier exists:* Subject to peer review or institutional methodology standards. Slower to update than Tier A, but strong on methodology quality and analytical rigor, making this tier the preferred source for benchmarks, models, and comparative urban analysis.

### Tier C — Industry & Professional Bodies
Real estate industry reports (e.g., CREDAI, Knight Frank, JLL, Anarock), professional associations, recognized NGOs with published methodology.

*Why this tier exists:* Often the only source for market-facing figures (transaction volumes, price indices) that government data reports with a lag. Usable, but commercially motivated — always cross-validated against Tier A/B where possible, and never used alone for a headline claim.

### Tier D — Reputed Media
Major national and regional newspapers, established business publications.

*Why this tier exists:* Useful for event-level facts (a project was announced, a road was inaugurated) and as a discovery mechanism pointing to the underlying official document — never as the final citation for a quantitative claim. If a news article cites a report, the report is sourced, not the article.

### Tier E — Unacceptable Sources (Prohibited)
Personal blogs, Wikipedia, Quora, Reddit, social media posts, AI-generated statistics, anonymous or unverifiable reports, SEO content farms, unsourced "best of" listicles.

*Why this tier exists:* No accountability, no methodology disclosure, no correction mechanism, and frequently no original authorship. Wikipedia and similar tertiary sources may be used only as a **discovery aid** to locate a primary source, and the primary source — never the tertiary page — is what gets registered and cited.

**Rule:** Tier A and Tier B are the default expectation for every core metric (population, boundaries, infrastructure, registrations, official growth plans). Tier C is acceptable for market-behavior figures when disclosed and cross-validated. Tier D is acceptable only for event confirmation. Tier E is never acceptable as a citation, under any circumstance, for any document in this repository.

---

## 4. Comprehensive Source Categories

Each category below defines: **Purpose**, **Typical Datasets**, **Preferred Sources**, **Validation Method**, and **Expected Update Frequency**.

### 4.1 Demographics
- **Purpose:** Population base for PIR, GSI, and demographic growth weighting in IAS.
- **Typical datasets:** Population counts by ward/locality, age structure, household size, literacy, migration.
- **Preferred sources:** Census of India (decennial + interim projections), NMC ward data, Directorate of Economics and Statistics, Maharashtra.
- **Validation:** Cross-check ward-level totals against NMC administrative counts; flag any locality without a matching census enumeration block.
- **Update frequency:** Census — decennial (with interim estimates); NMC administrative counts — annual.

### 4.2 Infrastructure
- **Purpose:** Core input to Infrastructure Density Index (IDI) and Infrastructure Score (30% of IAS).
- **Typical datasets:** Roads, water supply lines, schools, hospitals, public facilities, drainage networks.
- **Preferred sources:** NMC, NMRDA, Public Works Department (PWD) Maharashtra.
- **Validation:** Haversine-based coordinate proximity checks against locality centroids; locality_id foreign key integrity checks.
- **Update frequency:** Annual, or on major capital works completion.

### 4.3 Transportation
- **Purpose:** Feeds mobility-adjacent scoring and MII (Metro Influence Index, v2.0).
- **Typical datasets:** Road network geometry, transit routes, station locations, traffic volume.
- **Preferred sources:** Maha Metro, NMC Traffic Department, National Highways Authority of India (NHAI).
- **Validation:** Spatial validation against OSM road network; route-endpoint cross-check.
- **Update frequency:** Annual; immediate for new metro line/station openings.

### 4.4 Real Estate
- **Purpose:** Direct input to Price Growth Rate (35%) and Transaction Volume Growth (25%) in IAS.
- **Typical datasets:** Registered sale deeds, ready reckoner rates, price benchmarks.
- **Preferred sources:** IGR Maharashtra (Inspector General of Registration & Stamps), Maharashtra Ready Reckoner.
- **Validation:** Temporal consistency check (no negative growth artifacts from bad joins); duplicate transaction detection.
- **Update frequency:** Ready reckoner — annual; transaction registrations — continuous/quarterly extraction.

### 4.5 Economy
- **Purpose:** Macro context for market narrative and IAS calibration.
- **Typical datasets:** GDP/GSDP contribution, employment by sector, industrial output.
- **Preferred sources:** Directorate of Economics and Statistics (Maharashtra), Reserve Bank of India (RBI) regional data.
- **Validation:** Cross-source comparison with MIDC/MADC industrial data.
- **Update frequency:** Annual.

### 4.6 Industrial Development
- **Purpose:** Growth signal input (GSI) — industrial expansion is a leading indicator of locality demand.
- **Typical datasets:** Industrial estate boundaries, MIHAN cargo/SEZ activity, MIDC plot allotments.
- **Preferred sources:** MIDC, MADC (Maharashtra Airport Development Company).
- **Validation:** Boundary polygon cross-check against NMRDA land use maps.
- **Update frequency:** Annual, or on major allotment/announcement.

### 4.7 Urban Planning
- **Purpose:** Ground-truth for future zoning, land-use, and growth-corridor assumptions.
- **Typical datasets:** Development Plans (DP), Master Plans, zoning maps.
- **Preferred sources:** NMRDA, NMC Town Planning Department.
- **Validation:** Manual review against published DP documents; version-dated.
- **Update frequency:** Plan-cycle based (typically decade-scale); amendments tracked individually.

### 4.8 Healthcare
- **Purpose:** Core input to Healthcare Accessibility Score (HAS) — currently a documented v1.0 gap requiring resolution (see audit findings).
- **Typical datasets:** Hospital/PHC/clinic locations, bed capacity, specialty coverage.
- **Preferred sources:** National Health Mission (NHM) Maharashtra, NMC Health Department, Directorate of Health Services.
- **Validation:** Coordinate validation; deduplication against multiple listing sources.
- **Update frequency:** Annual.

### 4.9 Education
- **Purpose:** Locality quality-of-life context.
- **Typical datasets:** School/college locations, enrollment, affiliation boards.
- **Preferred sources:** UDISE+ (Unified District Information System for Education), Maharashtra State Board records.
- **Validation:** Coordinate + affiliation cross-check.
- **Update frequency:** Annual.

### 4.10 Environment
- **Purpose:** Risk-adjustment context (flood, pollution) for locality attractiveness.
- **Typical datasets:** Air quality index, green cover, water body health.
- **Preferred sources:** Maharashtra Pollution Control Board (MPCB), Central Pollution Control Board (CPCB).
- **Validation:** Station-level metadata check; temporal continuity.
- **Update frequency:** Continuous (AQI), annual (green cover surveys).

### 4.11 Water Supply
- **Purpose:** Infrastructure completeness input.
- **Typical datasets:** Supply network coverage, treatment plant capacity, connection counts.
- **Preferred sources:** NMC Water Works Department, Maharashtra Jeevan Pradhikaran (MJP).
- **Validation:** Coverage polygon vs. locality boundary overlay.
- **Update frequency:** Annual.

### 4.12 Drainage & Stormwater
- **Purpose:** Flood-risk-adjacent infrastructure scoring.
- **Typical datasets:** Drainage network maps, stormwater capacity, historical waterlogging points.
- **Preferred sources:** NMC Engineering Department.
- **Validation:** Spatial overlay against low-lying elevation zones.
- **Update frequency:** Annual, or post-monsoon incident update.

### 4.13 Electricity
- **Purpose:** Utility completeness signal.
- **Typical datasets:** Substation locations, transformer density, outage frequency.
- **Preferred sources:** Maharashtra State Electricity Distribution Co. Ltd. (MSEDCL).
- **Validation:** Coordinate validation; capacity cross-check.
- **Update frequency:** Annual.

### 4.14 Telecommunications & Digital Infrastructure
- **Purpose:** Connectivity signal for commercial/residential attractiveness.
- **Typical datasets:** Tower locations, broadband/fiber coverage, mobile network quality.
- **Preferred sources:** Department of Telecommunications (DoT), TRAI reports.
- **Validation:** Coverage-claim cross-check against independent speed-test aggregates (Tier C, disclosed methodology only).
- **Update frequency:** Annual.

### 4.15 Public Safety & Emergency Services
- **Purpose:** Quality-of-life scoring input.
- **Typical datasets:** Police station locations, fire station coverage, crime statistics.
- **Preferred sources:** Nagpur City Police, National Crime Records Bureau (NCRB).
- **Validation:** Coordinate validation; jurisdiction boundary cross-check.
- **Update frequency:** Annual (NCRB), continuous (station registry).

### 4.16 Climate
- **Purpose:** Long-range risk context.
- **Typical datasets:** Rainfall, temperature trends, extreme-event frequency.
- **Preferred sources:** India Meteorological Department (IMD).
- **Validation:** Station-metadata verification.
- **Update frequency:** Continuous/annual summaries.

### 4.17 Land Use & Land Records
- **Purpose:** Zoning legality and locality classification.
- **Typical datasets:** Land use maps, 7/12 extracts, property tax records.
- **Preferred sources:** NMRDA, Maharashtra Land Records Department (Bhulekh/Mahabhumi).
- **Validation:** Polygon-boundary cross-check against DP maps.
- **Update frequency:** On DP amendment; continuous for individual land records.

### 4.18 Property Registration
- **Purpose:** Direct transaction-volume and price input (see 4.4).
- **Typical datasets:** Sale deed registrations, stamp duty valuations.
- **Preferred sources:** IGR Maharashtra.
- **Validation:** Duplicate/void-transaction filtering.
- **Update frequency:** Continuous.

### 4.19 Zoning
- **Purpose:** Legal land-use classification per locality.
- **Typical datasets:** Residential/commercial/industrial/mixed-use zone maps.
- **Preferred sources:** NMRDA Development Plan.
- **Validation:** Cross-check against approved DP amendments.
- **Update frequency:** Plan-cycle based.

### 4.20 Commercial Activity
- **Purpose:** Locality economic-vibrancy signal.
- **Typical datasets:** Registered business density, GST registrations by pin code, retail footprint.
- **Preferred sources:** Ministry of Corporate Affairs (MCA), GST Network (aggregate/public disclosures only).
- **Validation:** Pin-code-to-locality mapping verification.
- **Update frequency:** Annual.

### 4.21 Tourism & Heritage
- **Purpose:** Locality identity and secondary economic context.
- **Typical datasets:** Heritage site listings, tourist footfall, protected monument boundaries.
- **Preferred sources:** Archaeological Survey of India (ASI), Maharashtra Tourism Development Corporation (MTDC).
- **Validation:** Boundary/coordinate cross-check.
- **Update frequency:** Low-frequency (static unless new designation).

### 4.22 Culture
- **Purpose:** Contextual/narrative enrichment, not scoring input.
- **Typical datasets:** Cultural institutions, event calendars.
- **Preferred sources:** NMC, MTDC, state cultural department.
- **Validation:** Manual review.
- **Update frequency:** Annual.

### 4.23 Employment
- **Purpose:** Economic demand signal.
- **Typical datasets:** Sector-wise employment figures, unemployment rate.
- **Preferred sources:** Directorate of Economics and Statistics, Periodic Labour Force Survey (PLFS).
- **Validation:** Cross-check against Census/economic survey figures.
- **Update frequency:** Annual.

### 4.24 Mobility & Traffic
- **Purpose:** Congestion and accessibility scoring.
- **Typical datasets:** Traffic volume counts, average speed data, junction-level congestion.
- **Preferred sources:** NMC Traffic Police, NHAI.
- **Validation:** Temporal consistency (peak/off-peak sanity checks).
- **Update frequency:** Annual survey, continuous where sensor data exists.

### 4.25 GIS, Satellite Imagery & Remote Sensing
- **Purpose:** Coordinate validation base layer; land-cover change detection.
- **Typical datasets:** Satellite raster imagery, land-cover classification, elevation models.
- **Preferred sources:** Bhuvan (ISRO), Survey of India, Sentinel/Landsat (public domain).
- **Validation:** Ground-truth spot-check against known landmarks.
- **Update frequency:** Varies by product (weeks to years).

### 4.26 Administrative, Political & Ward Boundaries
- **Purpose:** Canonical geographic reference for all locality joins.
- **Typical datasets:** Ward boundaries, municipal limits, constituency boundaries.
- **Preferred sources:** NMC, Election Commission of India, Survey of India.
- **Validation:** Topology check (no gaps/overlaps); authoritative boundary reconciliation.
- **Update frequency:** On delimitation/boundary revision only.

### 4.27 Census Blocks
- **Purpose:** Finest-grain demographic join key.
- **Typical datasets:** Enumeration block boundaries and counts.
- **Preferred sources:** Census of India.
- **Validation:** Boundary-to-ward containment check.
- **Update frequency:** Decennial.

### 4.28 OpenStreetMap & POIs
- **Purpose:** Supplementary spatial layer for validation and gap-filling — never a primary source for official statistics.
- **Typical datasets:** Points of interest, road centerlines, building footprints.
- **Preferred sources:** OpenStreetMap (used as Tier C/validation-only, methodology-disclosed community data).
- **Validation:** Used strictly to cross-validate Tier A/B data, never as a standalone citation for an official statistic.
- **Update frequency:** Continuous (community-maintained).

### 4.29 Public Transport, Metro, Airport, Railways, Highways, Bus Network
- **Purpose:** Direct infrastructure and MII input.
- **Typical datasets:** Station/stop locations, route maps, service frequency.
- **Preferred sources:** Maha Metro, MADC (airport), Indian Railways/Central Railway, NHAI, NMC bus operator (Nagpur Municipal Corporation Transport / Green Nagpur).
- **Validation:** Coordinate validation against official route maps.
- **Update frequency:** Annual; immediate on new line/route commissioning.

### 4.30 Walkability, Cycling, Parking
- **Purpose:** Locality livability scoring (future scope).
- **Typical datasets:** Footpath coverage, cycle lane length, parking capacity.
- **Preferred sources:** NMC Engineering Department, Smart City Nagpur.
- **Validation:** Field-survey cross-check where available.
- **Update frequency:** Annual.

### 4.31 Utilities (Solid Waste, Sewage, Stormwater)
- **Purpose:** Civic infrastructure completeness.
- **Typical datasets:** Waste collection coverage, sewage treatment capacity.
- **Preferred sources:** NMC Solid Waste Management Department.
- **Validation:** Coverage-area overlay check.
- **Update frequency:** Annual.

### 4.32 Flood Risk
- **Purpose:** Risk-adjustment layer for infrastructure and investment scoring.
- **Typical datasets:** Flood-prone zone maps, historical flood incident points.
- **Preferred sources:** NMRDA, Maharashtra State Disaster Management Authority.
- **Validation:** Cross-check against elevation and drainage data (4.12, 4.25).
- **Update frequency:** Annual, post-monsoon update.

### 4.33 Air Quality & Noise Pollution
- **Purpose:** Environmental quality-of-life scoring.
- **Typical datasets:** AQI readings, noise level monitoring.
- **Preferred sources:** MPCB, CPCB.
- **Validation:** Station metadata and continuity check.
- **Update frequency:** Continuous.

### 4.34 Green Spaces, Lakes, Rivers
- **Purpose:** Environmental and livability scoring.
- **Typical datasets:** Park boundaries, lake/river extents, water body health index.
- **Preferred sources:** NMC Garden Department, MPCB, NMRDA.
- **Validation:** Polygon overlay validation.
- **Update frequency:** Annual.

### 4.35 Crime
- **Purpose:** Safety-adjacent scoring (handled with strict aggregation to avoid stigmatizing individual localities on thin data).
- **Typical datasets:** Aggregated crime statistics by jurisdiction.
- **Preferred sources:** NCRB, Nagpur City Police.
- **Validation:** Jurisdiction-to-locality mapping validation.
- **Update frequency:** Annual.

### 4.36 Disaster Management
- **Purpose:** Risk-layer context.
- **Typical datasets:** Hazard zone maps, emergency shelter locations.
- **Preferred sources:** Maharashtra State Disaster Management Authority.
- **Validation:** Coordinate and boundary validation.
- **Update frequency:** Annual.

### 4.37 Economic Zones & Industrial Estates
- **Purpose:** Growth-corridor identification.
- **Typical datasets:** SEZ boundaries, industrial estate occupancy.
- **Preferred sources:** MIDC, MADC.
- **Validation:** Boundary cross-check against 4.6.
- **Update frequency:** Annual.

### 4.38 Future Infrastructure, Master Plans, Smart City Projects
- **Purpose:** Forward-looking GSI input — treated with elevated validation burden since these are plans, not facts on the ground.
- **Typical datasets:** Announced but unbuilt infrastructure, DP amendments, Smart City Mission project lists.
- **Preferred sources:** NMRDA, Smart City Nagpur (a Special Purpose Vehicle under NMC), MADC.
- **Validation:** Every "future" record must be tagged with project status (announced / approved / under construction / commissioned) and a source date; status must be re-verified at each research cycle, not assumed stable.
- **Update frequency:** Quarterly status re-check.

---

## 5. Nagpur-Specific Sources

| Organization | Type | Data Provided |
|---|---|---|
| **Nagpur Municipal Corporation (NMC)** | Municipal body | Ward boundaries, civic infrastructure, water supply, solid waste, health, property tax, local budgets |
| **Nagpur Metropolitan Region Development Authority (NMRDA)** | Planning authority | Development Plan, zoning, land use, metropolitan region boundaries |
| **Maharashtra Metro Rail Corporation (Maha Metro) — Nagpur Metro** | Transport agency | Metro station locations, ridership, route alignment, phase status |
| **Maharashtra Airport Development Company (MADC)** | Infrastructure SPV | MIHAN project data, cargo hub status, SEZ allotments |
| **Maharashtra Industrial Development Corporation (MIDC)** | Industrial authority | Industrial estate boundaries, plot allotments, occupancy |
| **Census of India / Office of the Registrar General** | National statistical authority | Population, household, literacy, migration data |
| **Inspector General of Registration & Stamps, Maharashtra (IGR Maharashtra)** | Registration authority | Property registrations, ready reckoner rates, stamp duty data |
| **Maharashtra Pollution Control Board (MPCB)** | Environmental regulator | Air/water quality, pollution monitoring |
| **Maharashtra State Electricity Distribution Co. Ltd. (MSEDCL)** | Utility | Electricity infrastructure, substations, outage data |
| **Nagpur City Police** | Law enforcement | Crime statistics, station locations |
| **Survey of India** | National mapping authority | Topographic maps, geodetic reference, administrative boundaries |
| **India Meteorological Department (IMD)** | National weather authority | Climate, rainfall, extreme events |
| **Directorate of Economics and Statistics, Maharashtra** | State statistical authority | Economic indicators, GSDP, employment |
| **Smart City Nagpur (SPV under NMC)** | Project implementation body | Smart City Mission projects, urban tech initiatives |
| **Central/Central Railway (Nagpur Division)** | Railway authority | Railway station data, rail network |
| **National Highways Authority of India (NHAI)** | Highway authority | National highway alignment, ring road status |

---

## 6. Dataset Documentation Standard

Every dataset entering the repository must carry the following metadata block, stored alongside the dataset (see §9 for folder structure):

```yaml
dataset_id: DS-0001
dataset_name: "Nagpur Ward-wise Population (2011 Census, 2024 Interim Estimate)"
category: Demographics
publisher: "Office of the Registrar General & Census Commissioner, India"
source_id: SRC-0012
publication_date: 2024-03-15
download_date: 2026-06-02
access_date: 2026-06-02
license: "Open Government Data License – India"
coverage: "Nagpur Municipal Corporation limits, all wards"
coordinate_system: "EPSG:4326 (WGS 84)"
file_format: CSV
version: v1.2
validation_status: PASS
last_verified: 2026-07-01
responsible_researcher: "Ruwaifa Ansari"
data_quality_score: 92/100
update_frequency: Decennial (with interim estimates)
known_limitations: "2011 base figures; interim estimate is a modeled projection, not a re-enumeration."
```

All fourteen fields are mandatory. A dataset missing any field fails validation and cannot be merged into a production table (see §10).

---

## 7. Source Registration Standard

Every source referenced anywhere in NagpurLens — whether it produces one dataset or fifty — is registered exactly once and receives a permanent **Source ID**.

**Format:** `SRC-NNNN` (four-digit, zero-padded, sequential, never reused).

- `SRC-0001` through `SRC-0999` — reserved for Tier A sources
- `SRC-1000` through `SRC-1999` — reserved for Tier B sources
- `SRC-2000` through `SRC-2999` — reserved for Tier C sources
- `SRC-3000` through `SRC-3999` — reserved for Tier D sources

Tier E sources are never registered; there is no ID range for them.

**Versioning:** If a source changes methodology, publisher, or scope materially, it is *not* edited in place — a new version is recorded as `SRC-0012-v2`, with `SRC-0012-v1` marked `DEPRECATED` and retained for audit trail.

**Archival policy:** Deprecated source entries are moved to `02_research/archive/sources_deprecated.md` with a deprecation reason and date, but are never deleted. Any dataset still referencing a deprecated Source ID is flagged in the next validation cycle for re-sourcing.

---

## 8. Citation Standard

### 8.1 In Markdown documents
Inline citations reference the Source ID in brackets, resolvable against `source_registry.csv`:

> The Nagpur Metro Phase I alignment covers approximately 38.2 km across two corridors [SRC-0007].

### 8.2 In SQL
Every table that stores a sourced value carries a `source_id` column (or a `source_map` join table for multi-source derived fields):

```sql
CREATE TABLE infrastructure (
    infra_id        INTEGER PRIMARY KEY,
    locality_id     TEXT REFERENCES localities(locality_id),
    infra_type      TEXT,
    latitude        REAL,
    longitude       REAL,
    source_id       TEXT NOT NULL,   -- e.g. 'SRC-0004'
    last_verified   DATE
);
```

### 8.3 In notebooks and scripts
A leading comment block declares all Source IDs consumed:

```python
# Sources consumed: SRC-0004 (NMC infrastructure registry), SRC-0012 (Census population)
```

### 8.4 In dashboards and reports
Every chart, table, or score panel carries a visible footnote listing the Source ID(s) behind it, and a hover/expand element showing the full citation (publisher, publication date, access date).

### 8.5 Full citation format (canonical, used in `source_registry.csv` and appendices)

```
[SRC-0012] Office of the Registrar General & Census Commissioner, India.
"Census of India 2011 — Nagpur District, Ward-wise Population."
Published: 2011-03-31. Accessed: 2026-06-02.
URL: <official census portal URL>
License: Open Government Data License – India.
```

---

## 9. Evidence Management

All raw evidence is stored, never only linked — links rot, evidence does not. Raw files (PDFs, government reports, GeoJSON, CSV/Excel exports, satellite imagery, photographs, planning documents, tender documents, master plans, annual reports, survey documents) are archived under a fixed structure:

```
02_research/
├── source_registry.csv
├── sources.md                     (this file)
├── assumptions.md
├── evidence/
│   ├── SRC-0001/
│   │   ├── original/              (raw, untouched download)
│   │   ├── processed/             (cleaned/derived version, if any)
│   │   └── metadata.yaml
│   ├── SRC-0002/
│   │   └── ...
│   └── ...
└── archive/
    └── sources_deprecated.md
```

Rules:
- The `original/` subfolder is **immutable** — files are never edited in place. Corrections produce a new dated file.
- Every evidence folder contains a `metadata.yaml` matching the Dataset Documentation Standard (§6).
- Large binary assets (satellite imagery, high-res scans) that exceed repository size limits are referenced via checksum + external storage pointer, with the checksum verified at each research cycle.

---

## 10. Data Validation Policy

Every dataset passes through the following checks before being marked `PASS` in its metadata:

1. **Cross-source validation** — where a second source exists, values are compared; discrepancies beyond a defined tolerance are flagged, not averaged.
2. **Coordinate validation** — Haversine-based nearest-centroid checks against locality boundaries; points falling outside all known localities are flagged `GEO-FAIL`.
3. **Duplicate detection** — record-level deduplication using natural keys (e.g., transaction ID, facility name + coordinate).
4. **Temporal validation** — publication and access dates are checked for plausibility (no future-dated evidence, no evidence older than its claimed publication date).
5. **Official confirmation** — for Tier C/D-derived facts, at least one Tier A/B corroboration is required before the value is used in a scoring formula (IAS, IDI, PIR, GSI, HAS, MII).
6. **Metadata validation** — all fourteen fields in §6 present and non-null.
7. **Spatial validation** — geometry passes topology checks (no self-intersecting polygons, no null island coordinates, no swapped lat/lon).
8. **Manual review** — a second contributor (not the original researcher) signs off before a dataset moves from `REVIEW` to `PASS`.
9. **Confidence scoring** — each dataset receives a PASS / REVIEW / FAIL confidence label per the project's established methodology, stored in its metadata and surfaced in the dashboard.

A dataset in `FAIL` status may not be merged into any production table.

---

## 11. Source Reliability Framework

Each registered source is scored across eight dimensions, 0–10 each (max 80, normalized to /100):

| Dimension | What It Measures |
|---|---|
| **Authority** | Is the publisher the legally/administratively responsible body for this data? |
| **Accuracy** | Historical track record of corrections/errata; internal consistency. |
| **Recency** | How current is the data relative to today's research cycle? |
| **Completeness** | Does it cover the full geography/time range needed, or are there gaps? |
| **Transparency** | Is methodology disclosed? Are raw figures available, not just summaries? |
| **Accessibility** | Is the data publicly retrievable, or does it require special access? |
| **Geographic Relevance** | Is it Nagpur/Maharashtra-specific, or extrapolated from national figures? |
| **Methodology Quality** | Sampling rigor, sample size, peer review status where applicable. |

**Quality thresholds:**
- **≥ 80/100** — Preferred source; usable without corroboration for headline metrics.
- **60–79/100** — Usable with mandatory cross-validation (§10.1).
- **40–59/100** — Usable only for context/narrative, never for scoring formulas.
- **< 40/100** — Not registered; treated as Tier E regardless of nominal tier.

---

## 12. Research Workflow

```
Research Question
        ↓
Identify Data Need & Target Category (§4)
        ↓
Locate Official/Preferred Source (§3, §5)
        ↓
Score Source Reliability (§11)
        ↓
Register Source → Assign Source ID (§7)
        ↓
Collect & Archive Raw Evidence (§9)
        ↓
Download Dataset
        ↓
Validate (§10)
        ↓
Document Metadata (§6)
        ↓
Clean & Transform
        ↓
Load to Database (with source_id references, §8.2)
        ↓
Cross-Reference in Dashboard / Report (with visible citation, §8.4)
        ↓
Log in source_registry.csv and Update Gap Tracker (assumptions.md)
```

This workflow mirrors the documentation process already established for the project: outline first, cross-reference against source documents, resolve contradictions before writing, then complete a structured self-review before finalizing.

---

## 13. Source Inclusion Rules

A source may be registered and used if **all** of the following hold:

- It belongs to Tier A, B, C, or D (§3).
- It scores ≥ 40/100 on the Reliability Framework (§11).
- The publisher and publication date are identifiable and stated.
- The data is legally accessible (public domain, open license, or properly licensed access — no paywalled scraping in violation of terms of service).
- The data is geographically or administratively relevant to Nagpur, the Nagpur Metropolitan Region, or Maharashtra state context feeding into a Nagpur-specific metric.
- It can be archived as evidence per §9 (a source that cannot be captured/retained cannot be verified later, and is therefore not usable for a permanent record).

---

## 14. Source Exclusion Rules

The following are permanently prohibited as citations, regardless of how compelling the figure appears:

- **Wikipedia, Quora, Reddit, and other user-editable/crowd-answer platforms** — no fixed authorship or accountability.
- **Personal or corporate blogs without disclosed methodology** — unverifiable provenance.
- **Social media posts** (including verified accounts) — not durable, not authoritative records.
- **AI-generated statistics** (including figures generated by any language model, this one included, without an underlying citation) — hallucination risk; a number an AI system states must never be treated as a fact until traced to a Tier A–D source.
- **Anonymous or unattributed reports** — no accountable publisher.
- **Content farms / SEO listicles** ("Top 10 areas to invest in Nagpur" style articles without disclosed data) — commercially incentivized, no methodology.
- **Any source scoring below 40/100** on the Reliability Framework, even if nominally Tier A–D.
- **Expired or unlicensed scraped data** obtained in violation of a source's terms of service.

If a contributor finds a critical fact only in a Tier E source, the correct action is to use it as a lead to locate the underlying primary source — not to cite it directly.

---

## 15. Research Ethics

- **No fabricated statistics.** If a number does not exist in a traceable source, it is recorded as a gap, not estimated silently.
- **No misleading visualizations.** Axes are not truncated to exaggerate trends; uncertainty (e.g., PASS/REVIEW/FAIL confidence) is visually represented, not hidden.
- **No selective reporting.** Contradicting data points are disclosed and reconciled, not dropped because they weaken a narrative.
- **Respect licensing.** Every dataset's license terms are recorded (§6) and honored, including attribution requirements and redistribution limits.
- **Open science principles.** Methodology, formulas (IAS, IDI, PIR, GSI, HAS, MII), and source registries are kept transparent and auditable by design.
- **Privacy considerations.** No individual-level personal data (names tied to property records, individual health data, etc.) is stored beyond what is already legally public and aggregated; all published figures respect statistical disclosure limits.
- **Transparency with limitations.** Every dataset's `known_limitations` field (§6) is treated as required reading before the data is used in a formula, not optional fine print.

---

## 16. Version Control

- **Sources** evolve via the versioning scheme in §7 (`SRC-NNNN-vX`); superseding a source never deletes its history.
- **Datasets** carry a `version` field (§6) incremented on any material change (new records, corrected coordinates, re-run validation).
- **Derived metrics** (IAS, IDI, PIR, GSI, HAS, MII) are versioned alongside the platform version sequence (v1.0–v4.0); a metric's formula and weighting are never changed silently within a version — changes require a new minor version and a changelog entry.
- **Deprecated sources** move to `02_research/archive/sources_deprecated.md` (§7) with reason and date; any dataset still depending on a deprecated source is flagged for re-sourcing before the next release.

---

## 17. Contributor Guidelines

Step-by-step checklist for adding a new source to NagpurLens:

1. Confirm the data need against the relevant category in §4 — check if a source is already registered for this category before searching for a new one.
2. Search using Tier A sources first (§3, §5); fall back to Tier B/C/D only if no official source exists.
3. Score the candidate source using the Reliability Framework (§11). If it scores below 40, stop — it cannot be used.
4. Assign the next available Source ID in the correct tier range (§7) and add an entry to `source_registry.csv`.
5. Download the raw file(s) and archive them under `02_research/evidence/SRC-NNNN/original/` (§9). Do not skip this step even for data you plan to re-derive — the original must exist independently of any transformation.
6. Complete the `metadata.yaml` for the evidence folder and the dataset metadata block (§6).
7. Run the dataset through the Data Validation Policy (§10). Do not proceed on a `FAIL`.
8. Request manual review from a second contributor (Mohammad Ammar or Zahid Khan, per current team roles) before marking `PASS`.
9. Add citations wherever the data is used — markdown (§8.1), SQL (§8.2), notebooks (§8.3), dashboards/reports (§8.4).
10. If the data reveals a gap relative to `VISION.md`, `PROBLEM.md`, or `three_questions.md`, log it explicitly in `assumptions.md` with a linked Source ID for the partial data that does exist.
11. Run through the Review Checklist (§18) before considering the source fully onboarded.

---

## 18. Review Checklist

Before any source or dataset is marked complete:

- ☐ Official or highest-available-tier publisher identified
- ☐ Source is publicly accessible (or licensed access documented)
- ☐ Relevant to a defined NagpurLens category (§4)
- ☐ Current — publication/update date within acceptable staleness for its category
- ☐ All fourteen metadata fields complete (§6)
- ☐ License terms recorded and compliant
- ☐ Source ID assigned in correct tier range (§7)
- ☐ Raw evidence archived under `02_research/evidence/` (§9)
- ☐ Reliability Framework score ≥ 40/100, recorded (§11)
- ☐ Cross-validated against a second source where one exists (§10)
- ☐ Coordinate/spatial validation passed, if geospatial (§10)
- ☐ Confidence status assigned: PASS / REVIEW / FAIL
- ☐ Reviewed and signed off by a second contributor
- ☐ Citations added everywhere the data is consumed (§8)
- ☐ Any resulting gap logged in `assumptions.md`

---

## 19. Glossary

- **Source** — An identifiable, registered publisher of data (an organization, institution, or official body), assigned a permanent Source ID.
- **Dataset** — A specific file or table derived from one or more sources, carrying its own metadata and version.
- **Evidence** — The archived raw material (files, scans, exports) proving a source's content at the time of access.
- **Validation** — The process of confirming a dataset's accuracy, completeness, and structural integrity before production use.
- **Metadata** — The structured descriptive fields (§6) attached to every dataset.
- **Data Provenance** — The documented chain from raw source to final published value.
- **Spatial Data** — Data with a geographic/coordinate component (points, lines, polygons).
- **Open Data** — Data published under a license permitting reuse, typically with attribution.
- **Coordinate Reference System (CRS)** — The spatial referencing standard (NagpurLens standardizes on EPSG:4326 / WGS 84) used to interpret latitude/longitude values.
- **Primary Source** — The original publisher/authority responsible for the data (e.g., NMC for its own infrastructure records).
- **Secondary Source** — A source reporting on data originated elsewhere (e.g., a newspaper article about a Census figure).
- **Research Integrity** — Adherence to accuracy, transparency, and non-fabrication standards throughout the research process.
- **Confidence Status** — The PASS / REVIEW / FAIL label assigned to a dataset reflecting its validation outcome.

---

## 20. Appendix

### 20.1 Recommended Directory Structure
```
02_research/
├── sources.md
├── source_registry.csv
├── assumptions.md
├── evidence/
│   └── SRC-NNNN/
│       ├── original/
│       ├── processed/
│       └── metadata.yaml
└── archive/
    └── sources_deprecated.md
```

### 20.2 Naming Conventions
- Source IDs: `SRC-NNNN` (tier-ranged, §7), versioned as `SRC-NNNN-vX`
- Dataset IDs: `DS-NNNN`, sequential, no tier range
- Locality IDs: three-digit `XXX` format, per existing project convention
- Evidence folders: named exactly by Source ID, no abbreviations

### 20.3 Example Source ID
```
SRC-0012  →  Office of the Registrar General & Census Commissioner, India
```

### 20.4 Example Citation
```
[SRC-0012] Office of the Registrar General & Census Commissioner, India.
"Census of India 2011 — Nagpur District, Ward-wise Population."
Published: 2011-03-31. Accessed: 2026-06-02.
License: Open Government Data License – India.
```

### 20.5 Example Metadata Record
See §6 for the full fourteen-field example (`DS-0001`).

### 20.6 Example Evidence Folder
```
02_research/evidence/SRC-0012/
├── original/
│   └── census_2011_nagpur_ward_population.csv
├── processed/
│   └── census_2011_nagpur_ward_population_cleaned.csv
└── metadata.yaml
```

### 20.7 Example Research Workflow (Applied)
Question: *"What is the current population of Locality 034 (Manish Nagar)?"*
1. Category: Demographics (§4.1)
2. Source: Census of India → NMC ward cross-reference
3. Reliability score: 88/100 → registered as `SRC-0012`
4. Evidence archived, metadata completed, validated (`PASS`)
5. Value loaded into `localities` table with `source_id = 'SRC-0012'`
6. Cited in dashboard population panel with visible footnote `[SRC-0012]`

---

*This document is binding for all NagpurLens contributors. Amendments require a version bump to this file and a corresponding changelog entry, following the same version-control discipline defined in §16.*
