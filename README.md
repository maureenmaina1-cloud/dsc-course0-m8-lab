# Aviation Accidents Analysis: Airline Safety Recommendations
### By: Maureen Maina
### For DSF-PT16M2

Business Problem

An airline/airplane insurer client wants to know which aircraft makes and models show low rates of total destruction and low likelihood of serious/fatal passenger injury in the event of an accident, along with any general risk factors that influence accident severity.

The analysis uses NTSB aviation accident data spanning 1948–2023, filtered to reflect the client's actual scope of interest (see Methodology). Recommendations are provided separately for small and large aircraft, since the two categories face very different operating conditions and risk profiles.

├── AviationData.csv   # Raw source data (NTSB)

├── Aviation_Accidents_Cleaning_Maureen_Maina_Done.ipynb # Data loading, cleaning, feature engineering

├── Aviation_Accidents_Data_Analysis_Maureen_Maina_Done.ipynb  # EDA, visualization, findings, recommendations

├── aviation_data_clean.csv   # Cleaned dataset (output of the cleaning notebook)

└── README.md

### **Methodology**
### Data Cleaning
Scope filters, matching the client's stated requirements:
- Aircraft.Category == 'Airplane' — client is only interested in airplanes.
- Amateur.Built == 'No' — client is only interested in professional builds.
- Event.Date >= 1983-01-01 — assumes a 40-year max aircraft lifetime

### Key derived measures:
- total_onboard — estimated occupants per flight, built by summing Total.Fatal.Injuries + Total.Serious.Injuries + Total.Minor.Injuries + Total.Uninjured.
- serious_fatal_fraction — the fraction of occupants seriously or fatally injured, normalizing injury severity across aircraft of very different sizes.
- Destroyed — binary flag for whether the aircraft was a total loss.
- Make_Model — a combined identifier, since raw Model labels are not unique across manufacturers.
- Only makes with ≥50 accident records were retained, to ensure make-level comparisons are statistically meaningful.

Additional cleaning: normalized inconsistent casing (Make, Model), consolidated duplicate category spellings (e.g. 'UNK'/'Unknown'), treated non-physical values (e.g. 0 engines) and placeholder "Unknown" labels as missing data, and folded rare categories into an 'Other' bucket to keep group comparisons statistically usable.

### **Analysis**
- Aircraft were split into small (< 20 estimated occupants) and large (≥ 20) groups, per the client's requested passenger threshold.
- All group-level comparisons (by make, model, and risk factor) enforced a minimum sample size (n ≥ 10) before being included in rankings, to avoid concluding one or two accidents.
- Two additional risk factors were investigated beyond make/model: Weather Condition and Phase of Flight.

### **Key Findings**
**Small Aircraft**
- Grumman/Schweizer, Stinson, and Luscombe had the lowest destruction rates (under ~2.5% of accidents resulted in total loss).
- Maule, McDonnell Douglas, and Boeing had the lowest serious/fatal injury fractions among small-aircraft makes.
- At the model level, the Maule M-5-210C and Diamond DA20-C1 recorded zero serious/fatal injuries across their accident histories (11 records each).

*Recommendation*: prioritize Maule, Grumman/Schweizer, and Stinson — this combination performs well on both injury severity and aircraft survivability.

**Large Aircraft**
- McDonnell Douglas stood out with a serious/fatal injury fraction under 1%, substantially lower than Boeing, Embraer, or Airbus (each ~10–18%).
- On destruction rate, Embraer, Boeing, and Mcdonnell Douglas performed similarly well (all under 12%); Airbus had the highest rate of the group (~17%).
- At the model level, only three models had sufficient accident history (≥10 records) to compare — all Boeing: the 777 (0% injury fraction, 16 accidents) was the standout, followed by the 767 (~8.7%) and 737 (~13.4%, largest sample size).

*Recommendation*: McDonnell Douglas offers the most consistent large-aircraft safety record; among specific models, the Boeing 777 performs best.

*No single manufacturer wins on every measure — the safest choice depends on whether the client prioritizes passenger injury outcomes or aircraft survivability.*

### **Factor 1: Weather Condition**

Accidents in IMC (Instrument Meteorological Conditions — poor visibility) are far more severe than in VMC (clear visibility):
- Destruction rate: 37.3% (IMC) vs. 7.3% (VMC) — roughly 5x higher.
- Injury fraction: 66.3% (IMC) vs. 23.6% (VMC) — roughly 3x higher.
- Backed by solid sample sizes (844 IMC accidents, 13,916 VMC accidents).

### **Factor 2: Phase of Flight**

- Highest risk — Maneuvering: 29.1% destruction rate, 35.9% injury fraction (n=127). Low-altitude, low-speed flying leaves little margin to recover from a problem.
- Lowest risk — Taxi and Landing: ~1% destruction and injury rates (n=94, n=1,108) — low-speed, ground-level phases involve little kinetic energy.

*Caveat: the Unknown phase category dominates the dataset (13,901 of ~16,310 records) and shows a high injury fraction (31.7%) despite a middling destruction rate. Since we don't know what phases this bucket actually contains, phase-of-flight conclusions should be read as directional rather than fully generalizable.*

**Limitations**
- Aircraft. Category was inconsistently recorded before 2008; filtering strictly to 'Airplane' excludes records where this field was simply never populated, which disproportionately affects the earlier years of the 1983+ window.
- The small/large split is based on estimated occupants per accident, not aircraft design capacity — a handful of "small" entries (e.g. a low-occupancy Boeing flight) reflect unusual circumstances (ferry/training flights) rather than typical passenger service.
- The large-aircraft sample is dominated by Boeing; other manufacturers (Mcdonnell Douglas, Embraer, Airbus) qualify at the make level but lack individual models with enough accident history (≥10 records) to compare directly.
- The large proportion of data that is N/A or Unknown dominates some variables that would be useful to understand and generalize outcomes better.
  
**How to Reproduce**
- Run Aviation_Accidents_Cleaning.ipynb top to bottom — this reads AviationData.csv and writes aviation_data_clean.csv.
- Run Aviation_Accidents_Data_Analysis.ipynb top to bottom — this reads aviation_data_clean.csv and reproduces all visualizations and findings above.

Author

**Maureen Maina**
