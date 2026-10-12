# NJTA EV Highway Charging Analytics (Proof of Concept)

## Executive Summary
This project delivers a data-driven infrastructure allocation model for the **New Jersey Turnpike Authority (NJTA)** to prioritize DC Fast-Charger deployment across service areas on the New Jersey Turnpike and Garden State Parkway. 

By correlating regional EV growth with peak highway traffic bottlenecks, the model translates county-level demand into site-specific charger targets for **21 highway service plazas**, modeling a total network requirement of **503 fast chargers**.

---

## Project Background & Motivation

The New Jersey Turnpike Authority (NJTA) is in charge of the New Jersey Turnpike and the Garden State Parkway—two of the busiest highways in the state. The goal of this project is to analyze data and come to a conclusion that can help NJTA form a data-driven business decision.

As an owner of a Tesla, I have come to be very passionate about electric vehicles and the way they are evolving the transportation industry. Because of this, I wanted to create a project for NJTA that focuses on electric vehicles.

### The Business Opportunity
At toll stations, NJTA collects a lot of data across their All-Electronic Tolling (AET) gantries. Through E-ZPass registration records (like the Green Pass program) and Automated License Plate Readers (ALPR), they can capture vehicle type and fuel profile data—which can be used to identify a vehicle as electric. With this data, NJTA can assess the population of electric vehicles that use the Turnpike and Parkway. Seeing as how the rate of EV ownership is rapidly increasing, it is likely that the data would tell NJTA that they are supporting more and more electric vehicles.

By knowing this, NJTA can make a data-driven business decision. On top of owning the Turnpike and Parkway, NJTA also owns the rest stops (Service Areas) along these highways. Because EV usage is increasing for NJTA, they can invest in high-speed DC Fast-Charging stations at these rest stops—especially those located along corridors with the highest EV traffic volume.

Supporting EV owners could lead to higher profits for NJTA. Additionally, it is worth noting that charging an electric vehicle takes 20–30 minutes—way longer than a quick 5-minute gas refill. Naturally, EV owners get out of their cars and interact with the stores at the rest stops. NJTA can leverage this foot-traffic and dwell-time data to negotiate higher lease rates with rest stop vendors—further increasing non-toll profits.

---

## Proof of Concept (POC) Scope & Data Sources

Unfortunately, I do not have access to live AET/E-ZPass or ALPR data due to Driver's Privacy Protection Act (DPPA) laws. For the purposes of this project, I utilized related public datasets to construct a proxy demand model. This project serves as a **Proof of Concept (POC)** to demonstrate how NJTA can execute this allocation framework using internal toll data.

The project addresses three core questions:
1. **Has EV usage increased in New Jersey?**
2. **Which highway corridors handle the highest estimated EV traffic?**
3. **Which highway service areas correspond to these high-demand corridors, and how many chargers should be built at each?**

### Datasets Used
* **NJDEP Historic AFV Reports (Dec 2019, Dec 2022, Dec 2025):** Public EV registration counts by county and ZIP code ([NJDEP Drive Green](https://dep.nj.gov/drivegreen/nj-ev-data/)). Used to quantify statewide EV growth.
* **NJDOT Traffic Count 2025 Dataset with AADTs:** Highway traffic volume measurements across state roadways ([NJDOT Traffic Data](https://dot.nj.gov/transportation/refdata/roadway/traffic.shtm)). Used to identify peak corridor bottlenecks (`max_aadt`).
* **NJTA Service Area Directory:** Compiled list of 21 rest stops across the Turnpike and Parkway, including highway name, milepost, county, and direction ([NJTA Service Areas](https://www.njta.gov/travel-resources/service-areas/)).

---

## Analytical Methodology

### 1. Corridor Demand Index (CDI)
To determine highway demand, county-level EV registration counts were combined with peak highway traffic volumes recorded by NJDOT sensors in that county:

$$\text{CDI} = \frac{\text{County 2025 EV Count} \times \text{Peak Highway AADT}}{1,000,000}$$

Dividing by $1,000,000$ rescales multi-billion raw products into a clean, human-readable demand score while preserving exact relative demand ratios.

### 2. Target Charger Allocation Formula
To convert abstract CDI scores into actionable, site-specific parking lot charger counts, each service area was assigned a target allocation based on its county's CDI score:

$$\text{Target Chargers} = \max\left(4, \text{round}\left(\frac{\text{CDI}}{100}\right)\right)$$

* **Scaling Factor ($\div 100$):** Scales regional demand to realistic, site-level parking capacity (e.g., premier interstate hubs vs. regional stops).
* **Baseline Floor ($\max(4, \dots)$):** Enforces a minimum baseline of 4 chargers per plaza to guarantee basic corridor connectivity across all highway stretches.

---

## Key Results & Infrastructure Allocation

* **Statewide Network Total:** **503 DC Fast-Charging Plugs** recommended across 21 highway service plazas (averaging ~24 chargers per plaza).
* **Tier 1 Expansion Hubs:** **Montvale** (Parkway) and **Vince Lombardi** (Turnpike) lead the state with **66 target chargers each**, driven by Bergen County's peak EV adoption and George Washington Bridge corridor traffic.
* **Middlesex Central Corridor:** Central transit plazas (**Molly Pitcher, Joyce Kilmer, Thomas Edison, Grover Cleveland, Cheesequake**) require **41 chargers each**.

---

## Project Structure & Jupyter Notebooks

* **`notebooks/01_ev_growth_analysis.ipynb`:** Assessed historic EV growth across New Jersey counties (2019–2025).
* **`notebooks/02_traffic_demand_overlay.ipynb`:** Ingested NJDOT AADT traffic data, extracted peak bottleneck traffic (`max_aadt`), and calculated the Corridor Demand Index (CDI) per county.
* **`notebooks/03_service_area_allocation.ipynb`:** Mapped service plazas (`service_areas.csv`) to county CDI scores, computed `target_chargers`, and exported final site allocations to `service_area_charger_allocation.csv`.