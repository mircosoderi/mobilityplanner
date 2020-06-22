# A planner for mobility services and infrastructures

This report was produced in the context of the Applied Data Science Capstone Project "The Battle of Neighborhoods", that is a part of the IBM Data Science Professional Certificate offered on Coursera, see https://www.coursera.org/specializations/ibm-data-science to learn more.

### Introduction/Business problem

In this project, I will try building a tool that could be used by public decision makers for planning the strengthening of mobility-related services. The tool highlights those areas where mobility services appear not to be adequate given the attractiveness of the area.

Main decisions to be taken when approaching the problem are: 
1) how to partition the territory? 
2) what to consider for quantifying the attractiveness of an area? 
3) what to consider for quantifying the mobility services of an area?
4) how to identify and highlight areas where an investment is needed?

##### How to partition the territory?

As for the partitioning of the territory, assuming the territory of interest delimited by a bounding box, my choice is to partition such territory into a set of equally-sized squared partitions, each having side equal to the 1% of the shortest side of the bounding box, with a parametric lower boundary (I find a reasonable default for it to be 100 m). 
Why not to partition following political/geographical boundaries or other criteria? Just because any smart partitioning bases on a-priori assumptions that impose an upper limit to the value-added of the project. 
Why to impose a minimum width/height to the partition? That’s because the public transport is subject to feasibility constraints, and therefore project results must characterize for a certain flexibility for avoiding that feasibility constraints could lead decision makers to deviate from the indications that the tool provides, possibly following criteria other from those that have inspired the project.

##### What to consider for quantifying the attractiveness of  an area? 

The very basic intuition is that the most an area is dense of things of any sort, the more it attracts people. The most basic measure of density of an area that comes to my mind is the mere counting of OSM nodes in the area. 
A further basic intuition is that not only the quantity, but also the quality of such things should be taken into consideration. The most basic measure of quality that comes to my mind is the mere counting of likes on venues in the area of interest, retrieved from Foursquare. 
Since one like bears much more information than a generic dot on a map, one could find it to be reasonable to weight likes the double, and it is what I do. 
Moving from both the quantification of density and attractiveness, I’ll cluster areas in very attractive, quite attractive, and not attractive, and I’ll also start making some visualization.

##### What to consider for quantifying the mobility services of an area?

As for the quantification of mobility services, the most basic measure that comes to my mind is the mere counting of stops of the public transport, retrieved from the OSM. While not very refined, the model is general enough to be applied as-is to any territory. 
A more refined model could encompass a weighted sum of stops based on the counting of arrivals and departures around the day, but it involves identification and exploitation of territory-specific data sources. As a branch of the project, I will do this for the city of Florence, using Tuscany Region GTFS, and I will offer in the notebook the possibility for the user to list her own GTFS feeds. 
In any case, due to its speed, frequency, and reliability, one should find it to be reasonable to weight public transport on rails the double of that on wheels, and it is what I’ll do. 
Also, one also could consider roads, car parks, bike sharing, and similar, retrieved from the OSM, and it is what I’ll do. In the notebook, the user will find this as an option, enabled by default. Based on the quantification of mobility services, I’ll cluster areas in very well served, well served, and poorly served, and I’ll make some visualization.

##### How to identify and highlight areas where an investment is needed?

In end, I’ll provide an intuitive, visual, representation of those areas that more than other need an empowering of mobility services. The most basic representation that comes to my mind is that of drawing a map of the territory, where each area is colored as follow:
1) let attractiveness be 0 for not attractive areas, 1 for attractive areas, 2 for very attractive areas;
2) let mobility be 0 for poorly served areas, 1 for well served areas, 2 for very well served areas;
3) let gap be mobility minus attractiveness
4) set color to red if gap = -2, orange if  -1, yellow if 0, green if 1, dark green if 2.

### Data

Data sources are:
1) the Open Street Map, and its related search services such as the Nominatim and the Overpass API, that are used for finding places by name so that the user could indicate the name of the city or territory or her interest instead of delimiting the bounding box through its geospatial coordinates, and also for getting the bounding box of a given place, counting the OSM nodes (points of any sort that can be seen on the map) located within a bounding box, and finding and counting mobility services such as stops of the public transport, car parks, bike rentals, and similar;
2) the Foursquare APIs, and specifically the venues search and likes endpoints, are used for identifying points of interest in a given area, and for quantifying their capability of attracting people, that together with the density of the map in a given area, lead to the quantification of the overall attractiveness of a given area.

Also to be noted here that the drawing of the map that is the final result of the project results from a merge of geographical data and representations gathered from the Open Street Map, and the coloring that comes from the computations performed in the context of this project.