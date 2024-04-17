# Bark beetle risk

Machado Nunes Romeiro et al. 2024 "Optimizing forest management in the face of bark beetle risk"

This README file was generated on 17/04/2024 by Joyce Machado Nunes Romeiro.
Last updated: 17/04/2024


-------------------
GENERAL INFORMATION
-------------------
**DOI:** `XXXXXX`

### Contact Information
- **Name:** Joyce Machado Nunes Romeiro
- **Institution:** Norwegian University of Life Sciences
- **Email:** [joyce.machado.nunes.romeiro@nmbu.no](mailto:joyce.machado.nunes.romeiro@nmbu.no)
- **ORCID:** [https://orcid.org/0000-0002-7262-0458](https://orcid.org/0000-0002-7262-0458)

### Funding
This project has received funding from the Norwegian Research Council through the Climate Smart Forestry Norway project (NFR 302701).


### Description of dataset: 

This repository contains the hypothetical forest stands used for the manuscript "Optimizing forest management in the face of bark beetle risk" and the script used to analyze bark beetle effects on rotation length and NPV.

### Summary of the paper:

The rising impact of the spruce bark beetle (Ips typographus L.) on Europe's forests is becoming a major concern, with climate change intensifying the problem. This situation has sparked discussion across Europe about adopting new, adaptive forest management strategies to mitigate the economic impacts on the forestry sector. Despite this, some regions have yet to fully embrace long-term strategies against bark beetle outbreaks from a climate change perspective. In our study, we examined the effects of integrating bark beetle risk into forest management strategies, considering both current and future climate change scenarios. Our findings indicate that reducing rotation length of low density stands with a high proportion of Norway Spruce and situated in more productive sites yields substantial economic advantages. Particularly, regions with a history of bark beetle outbreaks, like Vestfold in Norway, stand to gain significantly from early harvesting. The economic gain from harvesting earlier in this region is projected to increase nearly tenfold over the next 50 years under climate change scenarios. Additionally, we recommend considering the use of mixed tree species within forests as another adaptation strategy, to enhance forest resilience against bark beetle infestations and other natural disturbances.

--------------------
DATA & FILE OVERVIEW
--------------------
### File List:

<Scripts:>

- processing.R: This script is the exact code used for the manuscript. Code run with the two datasets provided.
				
<Datasets:>

- Standsforsimulation_new_2702.xlsx: the 300 stands used for the simulation. 
- Data_risk_27022023_schedules.csv: output from the forest simulator GAYA 2.0 (Strîmbu et al., 2023) for the 300 stands.

NOTE: In order to be able to run the script, save the script and the files in the same folder.

--------------------------------------------------------
DATA-SPECIFIC INFORMATION FOR: processing.R:
--------------------------------------------------------
### The script will generate the following outputs:

- summary_table_1: This table summarizes rotation differences and NPV gains by host character and density character. It aggregates the data and provides mean, minimum, and maximum values for rotation differences and NPV gains, grouped by these characteristics. The table is ordered by density character and host character.
- summary_table_2: Similar to summary_table_1, but this table focuses on the aggregation by age character (whether the stands are "old" or "young"). It also provides mean, minimum, and maximum values for rotation differences and NPV gains, grouped by age character.
- summary_table_3: This table aggregates rotation differences and NPV gains by site index (si). It provides mean, minimum, and maximum values for these metrics, grouped by site index.
- max_schedules_aggregated: This table provides aggregated values for NPV gain and rotation differences across a range of environmental conditions (temperature, precipitation) and stand characteristics (host character, density, age). It serves as a high-level view of how these factors interact under different scenarios.
- NPV gain (NOK) figure: This plot visualizes the Net Present Value (NPV) gain across different temperature and precipitation scenarios, faceted by host character, density character, and age character.
- Rotation Difference figure: This plot shows the rotation differences across the same environmental scenarios as the NPV gain figure, also faceted by host character, density character, and age character. 

-----------------------------------------
DATA-SPECIFIC INFORMATION FOR: Standsforsimulation_new_2702.xlsx
-----------------------------------------
### Table explanation

- Stand no: Identifier for a specific stands (300 in total)
- Site index spruce: Five different site index (SI) classes (productivities), i.e., SI class H40 = 23 m, 20 m, 17 m, 14 m and 11 m, where H40 is defined as the dominant height at 40 years breast height age
- TT (yr): Initial stand age
- Dominant height, main species (m): Indicates the average height of the tallest trees 
- N/ha tot: Number of trees per hectare in total across all species present in the stand.
- N/ha spruce: Number of spruce trees per hectare.
- N/ha pine: Number of pine trees per hectare.
- BA (m2/ha): Basal area per hectare. 
- V (m3/ha): Volume per hectare, measured in cubic meters.
- Density; High or low
- S%: Density
- Rel Density: Relative density
- Host tree (%): Norway spruce share
- Rel D mean: Mean relative density
- Age mean: The average age of trees in the stand

-----------------------------------------
DATA-SPECIFIC INFORMATION FOR: Data_risk_27022023_schedules.csv
-----------------------------------------
### Table explanation

- stand: Identifier for a specific stands (300 in total)
- schedule: Identifier for treatments.
- period: 12 periods of 5 years each.
- stand_age: Age of the stand in years.
- d (cm): Average diameter of the trees in centimeters.
- hDom (m): Height of the dominant trees in meters.
- h (m): Average height of trees in meters.
- S%: Density.
- Stock_density: Density of the stock in the area.
- HS: Norway spruce share
- host_share_categ: Category of host share (100, 50 or 10%).
- n (/ha): Number of trees per hectare.
- n-G (/ha), n-F (/ha), n-B (/ha): Number of trees per hectare by tree species.
- vol (m3/ha): Volume of wood per hectare in cubic meters.
- vol-G (m3/ha), vol-F (m3/ha), vol-B (m3/ha): Volume of wood per tree species.
- Treatment: Type of treatment applied (7=final harvest).
- vol-treatment (m3/ha): Volume affected by treatment.
- price (kr/m3): Price per cubic meter of wood in NOK.
- cost-cutting (kr/m3): Cost of cutting per cubic meter in NOK.
- cost-transport (kr/m3): Cost of transportation per cubic meter in NOK.
- cost-total (kr/m3): Total cost per cubic meter in NOK.
- cost-regeneration (kr/ha): Cost of regenerating the stand per hectare in NOK.
- Regeneration: Indicates if there was regeneration.
- net cash flow (kr/ha): Net cash flow per hectare in NOK.
- NPV total: Total Net Present Value
- mean_temp: Mean annual temperature in degrees celsius.
- annual_precip: Annual precipitation in millimeters.
