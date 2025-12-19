
# Latest Status (11/25)
I am in the midst of trying to identify missing data using the `scripts/missing_towns.py` script.

There are like 75 towns missing from the `data/rej_with_masssave_participation.geojson` aggregate file.

Roughly 5 of them were weeded out at earlier stages of the `scripts/process_data.py` script, that's fine.

Five towns is fine for the purposes of doing statewide analysis. 75 is not. That's about a quarter of the entire state that is missing. 

---

Take a look at the `find_geoids.py` script for my attempt at coming up with an algorithm for identifying the GeoIds in REJ_by_Census_Tracts_2025.geojson that are missing from the final, aggregated output stored in rej_with_masssave_participation_table.csv. 

General problem is that the GeoIDs that are missing from original REJ set actually *are* (technically) in the final aggregated set... they're just all line one digit off due to an issue with the way the MassSave KML files stored their data (I presume).

I think I'm pretty close - I've gotten a way to:
   1. Find close pairs/sets in original REJ data
   2. Find similarly close pairs/sets in final aggregated data

The final aggregated data is basing off of the census tract ids gathered via the `masssave_block_groups.csv` -> `masssave_tract_groups.csv` -> `rej_with_masssave_participation_table.csv` pipeline.

### Example: Harvard

The **final** data does not contain any tracts belonging to Harvard.

The **aggregated tract** data contains only one tract for Harvard: 25027761400

The **problem** is that the original REJ data splits that tract into **two tracts**: 25027761401 and 25027761402

The **result**? My final dataset doesn't contain any data on Harvard because the original REJ dataset is expecting 25027761401 and 25027761402.

This is kind of fucked because there really is no **pattern** or easy way to discern one. It just seems like there were changes in census tracts between the MassSave dataset creation and the State of MA REJ dataset creation.

### Solution

As I type this review, I realize that the current `scripts/find_geoids.py` script is actually doing this wrong. 

This is a sample output for Harvard:
```
--- Processing Group 144: ['25027761401', '25027761402'] ---
Beginning search for group representative GEOID: 25027761401 (suffix: 7761401) in MPO: Montachusett
Considering 518 candidate GeoIDs for representative GEOID: 25027761401
  No candidates found with suffix prefix '7761401'
  No candidates found with suffix prefix '776140'
  No candidates found with suffix prefix '77614'
  Found 3 candidates with suffix prefix '7761': ['25027761300', '25027761100', '25027761200']...
Best candidates for group ['25027761401', '25027761402']: ['25027761300', '25027761100', '25027761200']
```

This is saying that it:
   1) Found 25027761401 and 25027761402 in the original REJ set (great)
   2) It somehow then missed that 25027761400 is in the dataset (womp)

Another example: Sturbridge
```
--- Processing Group 143: ['25027758103', '25027758104'] ---
Beginning search for group representative GEOID: 25027758103 (suffix: 7758103) in MPO: Central Massachusetts
Considering 518 candidate GeoIDs for representative GEOID: 25027758103
  No candidates found with suffix prefix '7758103'
  Found 1 candidates with suffix prefix '775810': ['25027758101']...
Best candidates for group ['25027758103', '25027758104']: ['25027758101']
```

The data for Sturbridge in the `masssave_tract_groups.csv` file:
```
25027758101,Sturbridge,27.607999999999997,0.0,5
25027758102,Sturbridge,37.156800000000004,0.0,25
```

So it unfortunately missed that there was a 25027758102. 

Fortunately, I can say that this '...01', '...02' mapping to a '...03', '...04' actually *is* a pattern.

---

All that said, once I do chug my way through this problem I should be able to create a mapping file that dictates how to convert each of GeoIDs that **do not exist in the final aggregate file** into the corresponding GeoIDs listed in the original REJ file. That should do a decent job at mapping MassSave data to census tract data per the state of MA's REJ designations.



## Scrap Section
Let's do this: let's manually fix like 5 of these and see what we find out.

--
town: Harvard
REJ Tracts: 25027761401, 25027761402
MS Tracts: 25027761400

--
town: Sturbridge
REJ Tracts: 25027758103, 25027758104
MS Tracts: 25027758101, 25027758102

--
town: Dudley
REJ Tracts: 25027755101, 25027755102
MS Tracts: 25027755100, 25027755200

--
town: Charlton
[X] Incorrect Mapping [X]
REJ Tracts: 2502775.2.101, 2502775.2.102
MS Tracts: 2502775.6.101, 2502775.6.102

--
town: Milton
REJ Tracts: 25027744201, 25027744202
MS Tracts: 25027744101, 25027744102, 25027744200, 25027744300, 25027744400
[x2]:
REJ Tracts: 25027744103, 25027744104
MS Tracts: 25027744102

--
town: Shrewsbury
REJ Tracts: 25027739101, 25027739102, 25027739201, 25027739202, 25027739401, 25027739402
MS Tracts: 25027739100, 25027739200, 25027739300, 25027739400, 25027739500

--
town: Worcester
REJ Tracts: 25027731601, 25027731602, 25027731801, 25027731802
MS Tracts: 25027731300

--
town: Oakham*
REJ Tracts: 25027721103, 25027721104
MS Tracts: 25027721101
+ Extra MS Tract: 25027721102 | [!] Exists in MS but NOT in REJ, Map, or Final

* checking what the online data map says:
- 25027721101 is Oakham. Exists in REJ & MS. +Map.
- 25027721103 is Rutland. Exists in REJ. +Map. NOT: MS.
- 25027721104 is Rutland. Exists in REJ. +Map. NOT: MS.


--
