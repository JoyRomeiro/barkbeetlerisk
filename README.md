# bark beetle risk

Romeiro et al. "Optimizing forest management in the face of bark beetle risk"

This README file was generated on 17/04/2024 by Joyce Machado Nunes Romeiro.
Last updated: 17/04/2024


-------------------
GENERAL INFORMATION
-------------------

// DOI: XXXXXX
// Contact Information
     // Name: Joyce Machado Nunes Romeiro
     // Institution: Norwagian University of Life Sciences
     // Email: joyce.machado.nunes.romeiro@nmbu.no
     // ORCID: https://orcid.org/0000-0002-7262-0458
// Funding sources: the Norwegian Research Council through - the Climate Smart Forestry Norway project [NFR 302701]

// Description of dataset: 

This repository contains the hypothetical forest stands used for the manuscript "Optimizing forest management in the face of bark beetle risk" and the script used to analyze bark beetle effects on rotation length and NPV.

Summary of the paper:

The rising impact of the spruce bark beetle (Ips typographus L.) on Europe's forests is becoming a major concern, with climate change intensifying the problem. This situation has sparked discussion across Europe about adopting new, adaptive forest management strategies to mitigate the economic impacts on the forestry sector. Despite this, some regions have yet to fully embrace long-term strategies against bark beetle outbreaks from a climate change perspective. In our study, we examined the effects of integrating bark beetle risk into forest management strategies, considering both current and future climate change scenarios. Our findings indicate that reducing rotation length of low density stands with a high proportion of Norway Spruce and situated in more productive sites yields substantial economic advantages. Particularly, regions with a history of bark beetle outbreaks, like Vestfold in Norway, stand to gain significantly from early harvesting. The economic gain from harvesting earlier in this region is projected to increase nearly tenfold over the next 50 years under climate change scenarios. Additionally, we recommend considering the use of mixed tree species within forests as another adaptation strategy, to enhance forest resilience against bark beetle infestations and other natural disturbances.

--------------------
DATA & FILE OVERVIEW
--------------------
//File List: 
<Scripts:>

- processing.R: This script is the exact code used for the manuscript. Code run with the two datasets provided.
				
<Datasets:>

- Standsforsimulation_new_2702.xlsx: the 300 stands used for the simulation. 
- Data_risk_27022023_schedules.csv: output from the forest simulator GAYA 2.0 (Strîmbu et al., 2023) for the 300 stands.

NOTE: In order to be able to run the script, save the script and the files in the same folder.

--------------------------------------------------------
DATA-SPECIFIC INFORMATION FOR: processing.R:
--------------------------------------------------------
// Contextual Information:
<The script will estimate:>

pbb: Probability of bark beetle per plot
SMI:Soil Moisture Index
iBB: Damage Intensity
iBB_m3_ha: Damage Intensity in cubic meter per hectare (done only for NNFI.csv)
risk: Risk of bark beetle damage
risk_m3_ha: Risk of bark beetle damage in cubic meter per hectare (done only for NNFI.csv)


<The script will generate the following outputs:>

- CM5_CCLM_output.csv: same as CM5_CCLM.csv but the estimated 
- DMI_HIRHAM5_output.csv: same as DMI_HIRHAM5.csv, but the estimated pbb, SMI, iBB, risk are added at the end of the table
- EARTH_CCLM_output.csv: same as EARTH_CCLM.csv, but the estimated pbb, SMI, iBB, risk are added at the end of the table
- KNMI_RACMO_output.csv: same as KNMI_RACMO.csv, but the estimated pbb, SMI, iBB, risk are added at the end of the table
- MPI_CCLM_output.csv: same as MPI_CCLM.csv, but the estimated pbb, SMI, iBB, risk are added at the end of the table
- NFI_output.csv: same as NNFI.csv, but the estimated pbb, SMI, iBB, risk, iBB_m3_ha, risk_m3_ha are added at the end of the table
- boxplots for each climate model with pbb

-----------------------------------------
DATA-SPECIFIC INFORMATION FOR: Standsforsimulation_new_2702.xlsx
-----------------------------------------
// Variable/Column List: 
ID = Generic plot ID
year = years used to make estimation of future climatic conditions (from 2006 to 2080) 
mean_temp = mean annual temperature modeled with climate model CM5_CCLM
annual_precip = annual precipitation modeled with climate model CM5_CCLM
stand_age = generic stand age (set as 100 years old for all plots)
host_share_categ = generic norway spruce (host) share (set as 100% for all plots)
stock_density = generic stock density (set as 1 for all plots)
host_share_dummy = parameter used in the probability model
host_share_dummy_a = parameter used in the probability model
host_share_dummy_b = parameter used in the probability model

-----------------------------------------
DATA-SPECIFIC INFORMATION FOR: Data_risk_27022023_schedules.csv
-----------------------------------------

stand: Identifier for a specific stand of trees.
schedule: Schedule identifier for treatments or assessments.
period: Time period in years for data recording.
tpush: A code or flag related to operations (exact meaning not specified).
stand_age: Age of the tree stand in years.
d (cm): Diameter of the trees in centimeters.
hDom (m): Height of the dominant trees in meters.
h (m): Average height of trees in meters.
S%: Possibly a density or spacing percentage.
Stock_density: Density of the stock in the area.
HS: Host share, possibly a measure of biodiversity or specific species prevalence.
host_share_categ: Category of host share.
n (/ha): Number of trees per hectare.
n-G (/ha), n-F (/ha), n-B (/ha): Number of trees per hectare by category (G, F, B not specified).
vol (m3/ha): Volume of wood per hectare in cubic meters.
vol-G (m3/ha), vol-F (m3/ha), vol-B (m3/ha): Volume of wood per category.
Treatment: Type of treatment applied.
vol-treatment (m3/ha): Volume affected by treatment.
price (kr/m3): Price per cubic meter of wood.
cost-cutting (kr/m3): Cost of cutting per cubic meter.
cost-transport (kr/m3): Cost of transportation per cubic meter.
cost-total (kr/m3): Total cost per cubic meter.
cost-regeneration (kr/ha): Cost of regenerating the stand per hectare.
Regeneration: Indicates if there was regeneration (0 for no, other values might indicate yes).
net cash flow (kr/ha): Net cash flow per hectare.
NPV horizon: Net present value over a specific horizon.
NPV cycle end (T0), NPV landvalue (T0), NPV cycle end, NPV landvalue, NPV total: Various measures of net present value.
mean_temp: Mean annual temperature.
annual_precip: Annual precipitation in millimeters.
