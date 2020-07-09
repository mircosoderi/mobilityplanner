# A planner for mobility services and infrastructures

This report was produced in the context of the Applied Data Science Capstone Project "The Battle of Neighborhoods", that is a part of the IBM Data Science Professional Certificate offered on Coursera, see https://www.coursera.org/specializations/ibm-data-science to learn more.

### Introduction/Business problem

In this project, I try building a tool that could be used by public decision makers for planning the strengthening of mobility-related services. 

The tool highlights those areas where mobility services appear not to be adequate given the attractiveness of the area.

Main decisions to be taken when approaching the problem are: 
1) how to partition the territory? 
2) what to consider for quantifying the attractiveness of an area? 
3) what to consider for quantifying the mobility services of an area?
4) how to identify and highlight areas where an investment is needed?

##### How to partition the territory?

As for the partitioning of the territory, assuming the territory of interest delimited by a bounding box, my choice is to partition such territory into a set of equally-sized squared partitions, each having side equal to the 1% of the shortest side of the bounding box, with a parametric lower boundary (I find a reasonable default for it to be 500 m). 
Why not to partition following political/geographical boundaries or other criteria? Just because any smart partitioning bases on a-priori assumptions that impose an upper limit to the value-added of the project. 
Why to impose a minimum width/height to the partition? That’s because the building of infrastructures is subject to feasibility constraints, and therefore project results must characterize for a certain flexibility for avoiding that feasibility constraints could lead decision makers to deviate from the indications that the tool provides, possibly following criteria other from those that have inspired the project.

##### What to consider for quantifying the attractiveness of  an area? 

The very basic intuition is that the most an area is dense of things of any sort, the more it attracts people. The most basic measure of density of an area that comes to my mind is the mere counting of OSM nodes in the area. 
A further basic intuition is that not only the quantity, but also the quality of such things should be taken into consideration. The most basic measure of quality that comes to my mind is the mere counting of likes on venues in the area of interest, retrieved from Foursquare. 
Since one like bears much more information than a generic dot on a map, one could find it to be reasonable to weight likes the double, and it is what I do, giving the user to take different decisions anyway. Dedicated configuration parameters can be set indeed at the beginning of the notebook. 
Moving from both the quantification of density and attractiveness, I cluster areas in very attractive, quite attractive, and not attractive, and I also make some data visualization.

##### What to consider for quantifying the mobility services of an area?

As for the quantification of mobility services, the most basic measure that comes to my mind is the mere counting of map items related to both public and private transport, retrieved from the OSM. While not very refined, the model is general enough to be applied as-is to any territory. 
A more refined model could exploit territory-specific data sources, such as GTFS feeds, that are anyway left out of this project in its current release. 
One should find it to be reasonable to weight public transport more than private transport infrastructures and services, and it is what I do indeed. Dedicated configuration parameter can be found anyway at the beginning of the notebook so that the user could easily make her choice. 
Based on the quantification of mobility services, I cluster areas in very well served, well served, and poorly served, and I make some data visualization.

##### How to identify and highlight areas where an investment is needed?

In end, I provide an intuitive, visual, representation of those areas that more than other need an empowering of mobility services. The most basic representation that comes to my mind is that of drawing a map of the territory, where each area is colored as follow:
1) let attractiveness be 0 for not attractive areas, 1 for attractive areas, 2 for very attractive areas;
2) let mobility be 0 for poorly served areas, 1 for well served areas, 2 for very well served areas;
3) let gap be mobility minus attractiveness
4) set color to red if gap = -2, orange if  -1, yellow if 0, green if 1, dark green if 2.

### Data

Data sources are:
1) the Open Street Map, and its related search services such as the Nominatim and the Overpass API, that are used to ease the identification and representation of boundaries of the territory of interest, and to identify mobility-related items that locate within the boundaries of the territory of interest;
2) the Foursquare APIs, and specifically the venues search and likes endpoints, are used for identifying POIs in the territory of interest, and for quantifying their capability of attracting people (through their received likes), that together with the density of points (OSM nodes) on the map, lead to the quantification of the overall attractiveness of a given area.

Also to be noted here that the drawing of maps implies exploiting geographical data, rasters, and graphical artifacts, also gathered from the Open Street Map.

### Methodology

The analysis go through 14 steps that you can find well highlighted in the notebook. In the majority of them, you the user, just have to run cells. There are anyway cases where you have to make your hands dirty if you wish not only to watch what I have done, but also use the notebook for making this same analysis on a territory of your greater interest.

Below here a description of each step is provided:
1) Configuration, it is where the name of the city for which the analysis has to be conducted (I have typed "Montelupo Fiorentino", the town where I live), the minimum size of territory partitions, the relative weight of public and private transport, the relative weight of OSM nodes and FourSquare likes, and the FourSquare credentials are set;
2) Bounding box of the city, it is where Nominatim is queried for retrieving the bounding box of the city specified in configuration;
3) Bounding box partitioning, it is where the territory of interest is partitioned into small squares, each anyway having a minimum size as described above;
4) Open Street Map querying, it is where the instance of the Overpass API powered by Kumi Systems is invoked once for each partition of the territory, for identifying nodes (points on the map) and mobility-related items (public transport stops and other infrastructures, roads, parkings, rental services, and so on);
5) Open Street Map exploitation, it is where items identified at step 4 are used for making a quantification of the level of mobility services and infrastructures for each of the partitions of the territory. The quantification consists of a scalar that does not say so much in itself. At this step, the quantification of attractiveness is also started, based on the count of OSM nodes (points) that can be found within the boundaries of each partition;
6) Clustering by mobility, where the K-Means algorithm is used for partitioning (clustering) partitions into three sets, the set of those that are relatively well served, the set of those that are relatively quite well served, and the set of those that are relatively poorly served, as for the mobility;
7) Map (bind) cluster labels to mobility categories, it is where the centers of clusters are observed for understanding which quality levels correspond to the first, the second, and the third of generated clusters;
8) Map of mobility, it is where a map is drawn with colored dots that help understanding which areas of the territory have been considered to be relatively well served (green), quite well served (yellow), and poorly served (red). Open https://github.com/mircosoderi/mobilityplanner/blob/master/mobility_map_montelupo.png in this repository to display the map for the town of Montelupo Fiorentino, where I live;
9) FourSquare querying and exploitation, it is where the FourSquare venue search API is exploited for identifying 50 of the most relevant venues that can be found within the boundaries of the whole territory of interest. One also could replicate the query singularly for each partition of the territory of interest, but it leads to an explosion of requests, that are very likely not to match limitations imposed to trial accounts. In this same step, retrieved venues are mapped to partitions, and the FourSquare venue likes API is exploited for getting the count of likes obtained by each venue, that contribute to the quantification of the attractiveness of the partition where the venue locates;
10) Clustering by attractiveness, where the K-Means algorithm is used for partitioning (clustering) partitions into three sets, the set of those that are relatively well attractive, the set of those that are relatively quite well attractive, and the set of those that are not very attractive;
11) Map (bind) cluster labels to attractiveness levels, the same of step 7, repeated for attractiveness;
12) Map of attractiveness, it is where a map is drawn with colored dots that help understanding which areas of the territory have been considered to be relatively very attractive (green), quite well attractive (yellow), and not very attractive (red). Open https://github.com/mircosoderi/mobilityplanner/blob/master/attractiveness_map_montelupo.png in this repository to display the map for the town of Montelupo Fiorentino, where I live;
13) Gap computation, it is where the difference between the attractiveness and the level of mobility services and infrastructures is computed for each partition of the territory, taking into consideration not the quantifications (scalars), but the categorizations originated by clustering. See also paragraph "How to identify and highlight areas where an investment is needed?" above for further details;
14) Gap visualization, it is where a map is drawn with points over that, colored as indicated in paragraph "How to identify and highlight areas where an investment is needed?" above. Open https://github.com/mircosoderi/mobilityplanner/blob/master/gap_map_montelupo.png in this repository to display the map for the town of Montelupo Fiorentino, where I live.

### Results

It resulted that the center of Montelupo Fiorentino is relatively very well served as expected, but also its surrounding neighborhoods such as Ambrogiana, Erta, Samminiatello, Fibbiana, Capraia, and other, are quite well served. 

As for the attractiveness, the center of Montelupo Fiorentino but also some of its neighborhoods such as Samminiatello, Ambrogiana, Erta, Fibbiana can be considered very attractive. Limite can be considered very attractive also, despite the focus of the analysis is not over it.

Comparing mobility services and infrastructures with attractiveness, we find that there are no particular lacks, apart from in the East extremity of Limite.

### Discussion

Very interesting to note, clustering is demonstrated to be effective in answering questions such as "How much is 67? Is it a lot of a few?". The answer often is "It depends". It depends of other values, it depends of the context, and clustering is a straightforward way of associating some semantic to meaningless scalars, transforming absolute measurements to relative categorizations without personal biases. It is an alternative way of using clustering, that I have successfully experimented in this project.

### Conclusion

Despite some improvements appear to be recommended for usage in production real-world situations, such as adjusting minimum size of partitions of territory, seeking for venues at the partition level, exploiting data sources other such as GTFS feeds, and other, this simplified analysis has probably been anyway enough for validating the effectiveness of Open Street Map (and its related search services and APIs) as a data source from where a lot of value can be extracted in addition to traditional usages of geographical maps such as locating places, navigating, and so on, and the same can be said for FourSquare, that is anyway a paid service, while Open Street Map is free, open, collaborative. As mentioned above, a key aspect of the thing is also that clustering has demonstrated to be effective in translating absolute quantifications to relative ones _inline_ and _without personal biases_. When I say _inline_ I refer to the fact that one can perform the clustering in the context of a program execution, extracting value from data on the fly, even before the execution of the application terminates, and even before that some human could inspect data, analysis results, and make her considerations. 