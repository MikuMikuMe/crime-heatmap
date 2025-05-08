# crime-heatmap

Below is a Python program to visualize crime data on an interactive map using the `folium` and `pandas` libraries. This program assumes that you have crime data in a CSV file, which includes at least two columns: latitude and longitude.

Before running the script, ensure that you've installed the necessary packages. You can do this via pip:

```bash
pip install pandas folium
```

Now, you can write the Python program:

```python
import pandas as pd
import folium
from folium.plugins import HeatMap
import os

def load_data(file_path):
    """
    Load crime data from a CSV file.

    Parameters:
    file_path (str): The path to the CSV file.

    Returns:
    DataFrame: A pandas DataFrame containing the crime data.
    """
    if not os.path.exists(file_path):
        raise FileNotFoundError(f"The file {file_path} does not exist.")
    
    try:
        data = pd.read_csv(file_path)
    except pd.errors.ParserError as e:
        raise ValueError(f"Error parsing the CSV file: {e}")
    except Exception as e:
        raise Exception(f"An error occurred while reading the file: {e}")
    
    return data

def validate_data(data):
    """
    Validate that the data contains necessary columns for geolocation.

    Parameters:
    data (DataFrame): The crime data.

    Raises:
    ValueError: If the required columns are not present in the data.
    """
    required_columns = {'latitude', 'longitude'}
    if not required_columns.issubset(data.columns):
        raise ValueError(f"Data must contain the following columns: {required_columns}")

def create_heatmap(data, output_file="crime_heatmap.html"):
    """
    Create a crime heatmap and save it to an HTML file.

    Parameters:
    data (DataFrame): The crime data with latitude and longitude columns.
    output_file (str): The name of the output HTML file.
    """
    # Validate data has required columns
    validate_data(data)

    # Prepare heatmap data
    heatmap_data = data[['latitude', 'longitude']].dropna()

    # Create a map centered on the median location
    center_location = [heatmap_data['latitude'].median(), heatmap_data['longitude'].median()]
    crime_map = folium.Map(location=center_location, zoom_start=13)

    # Adding heat map
    HeatMap(data=heatmap_data, radius=15).add_to(crime_map)

    # Save map to HTML
    crime_map.save(output_file)
    print(f"Heatmap has been saved to {output_file}")

def main():
    """Main function to execute the program."""
    # Define the file path to the crime data CSV
    file_path = 'crime_data.csv'  # Change this to your actual file path

    try:
        # Load the data
        data = load_data(file_path)

        # Create and save the heatmap
        create_heatmap(data)

    except Exception as e:
        print(f"An error occurred: {e}")

if __name__ == '__main__':
    main()
```

### Explanation:
- **Data Loading**: We load data from a CSV file. Error handling ensures that the file is present and properly formatted.
- **Data Validation**: We check that the required columns for geolocation (`latitude` and `longitude`) exist. If not, we raise a `ValueError`.
- **Heatmap Creation**: We use `folium.Map` to create an interactive map centered on the median of the data points. The `HeatMap` plugin is used to plot the data.
- **Error Handling**: The program checks for file existence, CSV parsing errors, and validated data columns with appropriate error messages.

Make sure you have a file named `crime_data.csv` with columns `latitude` and `longitude` for this program to run appropriately. Customize the file path as necessary.