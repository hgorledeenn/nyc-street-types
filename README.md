# Manhattan Street Types
Created by **[Holden Green](https://hgorledeenn.github.io)** in March 2026 <br>
Columbia Journalism School, Data Studio

## Contents:
1. [The Project](#the-project)
2. [Data Wrangling](#data-wrangling)
3. [Visualiztion](#data-visualization)

<br>

## The Project
The main goal of this assignment was to get hands-on experience using the [ai2html](http://ai2html.org) tool to help make responsive visualizations. I decided to try my own version of a story that does a similar analysis on [street types in Moscow](https://medium.com/data-science/creating-a-map-of-street-designations-with-geopandas-and-matplotlib-7b40e9210737).

Specifically, Manhattan is known for its numbered grid of streets and avenues, and I though it would be interesting to visualize this (and the various other road types in the borough).

<br>

## Data Wrangling
I got the [shp file](/data/DCM_StreetCenterLine.shp) from the NYC [Digital City Map](https://www.nyc.gov/content/planning/pages/resources/datasets/digital-city-map) and imported it as a geopandas dataframe.

I used my own knowledge of American street types (as well as some research and about 30 minutes scrolling around Google Maps of the city) to create a list of street types I would try to identify in each street's name.

``` python
street_types = ['avenue', 'boulevard', 'bridge', 'court', 'concourse', 'drive', 'expressway', 'highway', 'lane', 'parkway', 'plaza', 'road', 'row', 'street', 'terrace', 'turnpike', 'way']
```

I then defined a function that returned any words in each street's name that match my list of designated street types, and applied it to my street name column.

``` python
def street_types_analysis (words):
    matches = [word for word in words if word in street_types]
    return " ".join(matches)

df['ind_street_type'] = df['Street_NM'].str.split().apply(street_types_analysis)
```

Lastly, I had to settle any occurrences of streets containing more than one of my designated street types (like in the case of the street named "adams street - brooklyn bridge boulevard", which contains 3 of my selected types).

I ultimately decided to simply choose the most common street type to be the sole identifier for any street with multiple matching words. I found the most common types in my dataset with a simple `.value_counts()` line and then defined my conditions and values and used the `np.select` function to assign new values.

``` python
street_type_conditions = [
    (df['ind_street_type'].str.contains('avenue')),
    (df['ind_street_type'].str.contains('street')),
    (df['ind_street_type'].str.contains('road')),
    (df['ind_street_type'].str.contains('boulevard')),
    (df['ind_street_type'].str.contains('court')),
    (df['ind_street_type'].str.contains('drive')),
    (df['ind_street_type'].str.contains('lane')),
    (df['ind_street_type'].str.contains('parkway')),
    (df['ind_street_type'].str.contains('terrace')),
    (df['ind_street_type'].str.contains('expressway')),
    (df['ind_street_type'].str.contains('highway')),
    (df['ind_street_type'].str.contains('way')),
    (df['ind_street_type'].str.contains('plaza')),
    (df['ind_street_type'].str.contains('bridge')),
    (df['ind_street_type'].str.contains('turnpike')),
    (df['ind_street_type'].str.contains('row')),
    (df['ind_street_type'].str.contains('concourse'))
]

street_types_ordered = ['avenue', 'street', 'road', 'boulevard', 'court', 'drive', 'lane', 'parkway', 'terrace', 'expressway', 'highway', 'way', 'plaza', 'bridge', 'turnpike', 'row', 'concourse']

df['street_type_final'] = np.select(street_type_conditions, street_types_ordered, default="other")
```

<br>

## Visualization
The main force for this project was to practice the technical aspects of using ai2html. I did get some very helpful hands-on experience using the tool and especially with debugging when things went wrong.

While I did successfully improve the graphics from the raw plotnine output and make them responsive to still appear nicely on mobile, I did not quite give myself enough time to make the graphics as polished as I would've liked them.

I have prior experience with the Adobe suite and was able to pretty quickly make the graphics nicer in Illustrator, but it took a lot of debugging to make them appear correctly on the page. Thankfully, I know a lot more now about how to do a project like this, and I am confident that future attempts would be much quicker.