The current directory contains a personal passion project for working with open data provided by the State of Massachusetts, specifically data on energy efficiency programs and environmental justice regions.

**Project Goal:** Perform a cross-sectional data analysis of the State of MA's MassSave energy efficiency program as compared to Regional Environmental Justice areas to identify whether there are any significant differences in participation data in REJ areas versus non-REJ areas. 

**Project Structure:** The project is currently centered around Python scripts performing data analysis and visualization tasks. Eventually, I would like to deploy this as a full-stack web application for others to view. 

## Data Included
All data is located in the `data/` folder and can be described as follows:
- `masssave_kmls/` - directory containing KML files downloaded from https://www.masssavedata.com/Public/GoogleEarth
- `masssave_kmls_unzipped/` - directory containing unzipped KML files for easier processing
- `REJ_by_Census_Tracts_2025.geojson` - GeoJSON file containing Regional Environmental Justice area data by census tract
- `masssave_block_groups.csv` - CSV file containing pre-processed MassSave participation data aggregated to census block group level (intermediate data)
- `rej_with_masssave_participation.geojson` - GeoJSON file containing merged REJ and MassSave participation data by census tract (final output data)
- `masssave_participation_map.html` - HTML file containing a map visualization of the merged data using Geopandas and Folium
- `final_town_output.txt` - The output of all distinct towns from a a CSV query of rej_with_masssave_participation.csv which is a result of the *left join* in the process_data script
- `missing_towns.txt` - A list of missing towns (not fully updated)
- `all_towns.txt` - List of all towns in State of MA


**Notes on Data:**
- MassSave KML files are separated per municipality in the State of MA and can be downloaded freely from: https://www.masssavedata.com/Public/GoogleEarth

## Scripts Explanation
All Python scripts included in the `scripts/` directory are here: 
1. `download_kmls.py` - downloads participation data KML files from the MassSave website
2. `process_data.py` - aggregate MassSave KML data, then merge with REJ dataset
3. `visualize_data.py` - use Geopandas + Folium to display the output file from `process_data.py` on a map of the state of MA
4. `preview_table.py` - Python Dart table to sort + filter through overlaid MassSave + REJ dataset
5. `missing_towns.py` - A working file to identify the towns missing in the dataset

---

## Big Picture Architecture
1. **Data Flow**: The project processes raw KML files (`masssave_kmls/`), aggregates them into intermediate CSV data (`masssave_block_groups.csv`), merges with REJ data (`REJ_by_Census_Tracts_2025.geojson`), and outputs a final GeoJSON file (`rej_with_masssave_participation.geojson`). This data is visualized as an interactive map (`masssave_participation_map.html`).
2. **Script Relationships**: Each script builds on the output of the previous one. For example, `process_data.py` depends on the output of `download_kmls.py`, and `visualize_data.py` uses the output of `process_data.py`.

## Critical Developer Workflows
1. **Setup**:
   - Install dependencies: `pip install -r requirements.txt` (ensure `geopandas`, `pandas`, `requests`, `beautifulsoup4`, and `dash` are installed).
2. **Running Scripts**:
   - Download KML files: `python scripts/download_kmls.py`
   - Process data: `python scripts/process_data.py`
   - Visualize data: `python scripts/visualize_data.py`
3. **Debugging**:
   - Use print statements or a debugger (e.g., `pdb`) to trace issues in data processing.
4. **Testing**:
   - Validate intermediate outputs (e.g., `masssave_block_groups.csv`) for correctness before proceeding to the next step.

## Project-Specific Conventions
1. **File Naming**: KML files are named by municipality. Ensure consistent naming when adding new files.
2. **Data Formats**: Use GeoJSON for spatial data and CSV for tabular data.
3. **Visualization**: Folium is used for map rendering; ensure compatibility with GeoJSON inputs.

## Integration Points
1. **Dependencies**:
   - `geopandas` for spatial data processing.
   - `folium` for map visualization.
   - `dash` for potential future web deployment.
2. **External Data**:
   - MassSave KML files from https://www.masssavedata.com/Public/GoogleEarth.
   - REJ GeoJSON data from state-provided sources.
