# NJTA EV Highway Charging Analytics (Proof of Concept)

## What This Project Is About
The New Jersey Turnpike Authority (NJTA) manages the New Jersey Turnpike and the Garden State Parkway—two of the busiest highways in the state. The goal of this project is to use public data to build a practical, data-driven model that helps NJTA figure out where they should put electric vehicle chargers.

As a Tesla owner, I’m pretty passionate about EVs and how they're changing transportation. Because of that, I wanted to build a project specifically focused on helping the NJTA tap into the EV boom.

### The Business Case
Right now, NJTA tracks traffic and vehicle types using All-Electronic Tolling (AET) gantries. Through things like E-ZPass registration records (like the Green Pass program) and license plate readers (ALPR), they can easily spot which cars are electric. 

Since EV ownership is growing fast, NJTA's data is going to show more and more electric cars passing through. Because NJTA also owns all the rest stops (Service Areas) along the Turnpike and Parkway, they have a huge opportunity: they can install fast-charging stations at these rest stops where EV traffic is heaviest.

Here’s why it's a win-win for NJTA:
* **Non-Toll Revenue:** Charging an EV takes 20 to 30 minutes (way longer than a quick 5-minute gas stop). That means drivers will actually get out of their cars and hang out at the rest stop stores. 
* **Better Vendor Deals:** NJTA can use that extra foot traffic and longer dwell time to negotiate higher lease rates with the rest stop shops and restaurants, boosting their profits outside of toll money.

---

## The Catch & How I Built It
Unfortunately, I don't have access to live E-ZPass or toll data because of privacy laws (DPPA). So for this proof of concept (POC), I used related public datasets and made some estimates to fill in the blanks.

To make it work, I broke the problem down into three main questions:
1. **Has EV usage increased in New Jersey?**
2. **Which highway corridors handle the highest estimated EV traffic?**
3. **Which highway service areas correspond to these busy spots, and how many chargers should we put at each?**

### The Public Data Used
* **NJDEP EV Reports (2019, 2022, 2025):** Public numbers on how many EVs are registered in each NJ county ([NJDEP Link](https://dep.nj.gov/drivegreen/nj-ev-data/)).
* **NJDOT Traffic Counts (2025):** Daily traffic volume measurements across state roads ([NJDOT Link](https://dot.nj.gov/transportation/refdata/roadway/traffic.shtm)) to find peak bottleneck spots (`max_aadt`).
* **NJTA Service Area Directory:** A list of the 21 rest stops along the Turnpike and Parkway with their locations and mileposts ([NJTA Link](https://www.njta.gov/travel-resources/service-areas/)).

---

## How the Math Works

1. **Corridor Demand Index (CDI):** 
   To figure out where the demand is highest, I multiplied each county's 2025 EV count by its peak highway traffic score, then divided by 1,000,000 just to keep the numbers easy to read:
   $$\text{CDI} = \frac{\text{County EVs} \times \text{Peak Highway Traffic}}{1,000,000}$$

2. **Target Charger Formula:**
   To turn those raw demand scores into an actual number of parking lot chargers, I scaled the CDI down and set a safety minimum:
   $$\text{Target Chargers} = \max\left(4, \text{round}\left(\frac{\text{CDI}}{100}\right)\right)$$
   * Dividing by 100 scales the demand down to realistic rest-stop sizes.
   * The $\max(4, \dots)$ ensures that even quieter, rural rest stops get at least 4 chargers so nobody gets stranded.

---

## The Results
* **Total Network Need:** The model calls for **503 fast-charging plugs** spread across all 21 service plazas.
* **Top Hubs:** **Montvale** (on the Parkway) and **Vince Lombardi** (on the Turnpike) need the most help—**66 chargers each**—because Bergen County has massive EV numbers and brutal commute traffic.
* **Central Corridor:** Busy central spots like **Molly Pitcher, Joyce Kilmer, Thomas Edison, Grover Cleveland, and Cheesequake** all land at **41 chargers each**.

---

## Project Structure
* **`notebooks/01_ev_growth_analysis.ipynb`:** Looked at how fast EVs grew across NJ counties from 2019 to 2025.
* **`notebooks/02_traffic_demand_overlay.ipynb`:** Pulled in traffic data, found the busiest highway spots, and calculated the demand scores.
* **`notebooks/03_service_area_allocation.ipynb`:** Matched those scores to actual rest stops, calculated the target charger counts, and saved the final CSV (`service_area_charger_allocation.csv`).