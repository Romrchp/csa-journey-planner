# Journey Planning in Zürich with the Earliest Connection Scan Algorithm

## Video presentation

A video presentation, containing a small part about validation and visualization, is available at the following link :
https://youtu.be/V4pXFV9UGCs 

----
## Problem Motivation

Imagine you are a regular user of the public transport system, and you are checking the operator's schedule to meet your friends for a class reunion.
The choices are:

1. You could leave in 10mins, and arrive with enough time to spare for gossips before the reunion starts.

2. You could leave now on a different route and arrive just in time for the reunion.

Undoubtedly, if this is the only information available, most of us will opt for option 1.

If we now tell you that option 1 carries a fifty percent chance of missing a connection and be late for the reunion. Whereas, option 2 is almost guaranteed to take you there on time. Would you still consider option 1?

Probably not. However, most public transport applications will insist on the first option. This is because they are programmed to plan routes that offer the shortest travel times, without considering the risk factors.

----
## Problem Description

In this final project we built your own _robust_ public transport route planner, using the SBB dataset (See next section: [Dataset Description](#dataset-description)).

Given a desired arrival time, the route planner computes the fastest route between departure and arrival stops within a provided confidence tolerance expressed as interquartiles.
For instance, "what route from _A_ to _B_ is the fastest at least _Q%_ of the time if I want to arrive at _B_ before instant _T_". Note that *confidence* is a measure of a route being feasible within the travel time computed by the algorithm.

The output of the algorithm is a list of routes between _A_ and _B_ and their confidence levels. The routes are sorted from latest (fastest) to earliest (longest) departure time at _A_, they must all arrive at _B_ before _T_ with a confidence level greater than or equal to _Q_. We also made it possible to visualize the routes on a map with straight lines connecting all the stops traversed by the route.

Solving this problem accurately can be difficult. We were thus allowed a few **simplifying assumptions**:

- We only considered journeys at reasonable hours of the day, and on a typical business day, and assuming a recent schedule.
- We allowed short (total max 500m "As the Crows Flies") walking distances for transfers between two stops, and assumed a walking speed of _50m/1min_ on a straight line, regardless of obstacles, human-built or natural, such as building, highways, rivers, or lakes.
- We only considered journeys that start and end on known station coordinates (train station, bus stops, etc.), never from a random location. However, walking from the departure stop to a nearby stop is allowed.
- We only considered departure and arrival stops in a 15km radius of Zürich's train station, `Zürich HB (8503000)`, (lat, lon) = `(47.378177, 8.540192)`.
- We only considered stops in the 15km radius that are reachable from Zürich HB. If needed stops may be reached via transfers through other stops outside the 15km area.
- We assumed that delays or travel times on the public transport network of **two different lines** are uncorrelated with one another, but **however** tried to capture dependencies of stops on the same route, e.g. if a train is late at a stop, it is expected to be late at subsequent stops.
- Once a route is computed, a traveller is expected to follow the planned routes to the end, or until it fails (i.e. miss a connection).
  We **did not** need to address the case where travellers are able to defer their decisions and adapt their journey "en route", as more information becomes available. This would require us to consider all alternative routes (contingency plans) in the computation of the uncertainty levels, which is more difficult to implement.
- The planner does not mitigate the traveller's inconvenience if a plan fails. Two routes with identical travel times under the uncertainty tolerance are equivalent, even if the outcome of failing one route is much worse for the traveller than failing the other route, such as being stranded overnight on one route and not the other.
- All other things being equal, routes with the minimum walking distance, and then minimum number of transfers are prefered.
- **We did not need to optimize the computation time of out method, as long as the run-time was reasonable, which is the case for our adapted ECSA.**
- When computing a path we picked the timetable of the most recent week and assumed that it was unchanged.

----
## Dataset Description

For this project we used data published on the [Open Data Platform Mobility Switzerland](<https://opentransportdata.swiss>).
The full description of the data is available in the opentransportdata.swiss data [istdaten cookbooks](https://opentransportdata.swiss/en/cookbook/actual-data/).

#### Timetable data

Timetable data are available from [timetable](https://opentransportdata.swiss/en/cookbook/gtfs/).

You will find there the timetables for the years [2018](https://opentransportdata.swiss/en/dataset/timetable-2018-gtfs), [2019](https://opentransportdata.swiss/en/dataset/timetable-2019-gtfs) and [2020](https://opentransportdata.swiss/en/dataset/timetable-2020-gtfs) and so on.
It was assumed that the weekly changes were small, and a timetable for a given week is thus the same for the full year.

The full description of the GTFS format is available in the opentransportdata.swiss data [timetable cookbooks](https://opentransportdata.swiss/en/cookbook/gtfs/).

## Repository Structure

- 📂 [data](data/) Contains resulting CSV tables from PySpark Queries of the SBB data, i.e Big Data preprocessing work used for the evaluation of our algorithm (Part 1 & 2 of the final notebook)

- 📂 [figs](figs/) Images and figures used throughout our work, mainly for explanations and clarity.

- 📂 [notebooks](notebooks/) Folder containing the notebook(s) containing the workdone for the project.
  - 📒 [final-journey_planner.ipynb](notebooks/final-journey_planner.ipynb) Main notebook, contains the entirety of the work done for the project.

- 📂 [renku-docker-relics](renku-docker-relics/) Contain relic files from the original implementation of the project, through the Renku platform. As this is a semester project, these files have become obsolete and have to be adapted again.

- 📄 [environment.yml](environment.yml) : File containing the different requirements for [final-journey_planner.ipynb](notebooks/final-journey_planner.ipynb) to run properly.

----
## HOW-TO

All of the work we made for the project is located in the `notebooks/final-journey-planner.ipynb` Jupyter notebook. However, before lauching this notebook and excecuting
the cells, a few things need to be taken into account :

- Please run the `git lfs pull` command in a terminal. From explanatory figures to actual datasets used in the project, we stored different things on git lfs and the cells will output errors without using this command before anything. If you opened the notebook before running the `git lfs pull`, please close and re-open it for the different figures to appear. 

- **As specified in the previous section, the Renku platform unfortunately isn't available anymore for this project. Parts 1 and 2 are therefore not going to run properly and are currently present for informative purposes about our approach to the project, but will be made runnable as soon as possible.**

- The important package folium (needed for map visualization) should be in the `requirements.txt` file. However, if it is not imported when installed with the session's launch, please run the `pip install folium` command in an empty notebook or at the beginning of the `notebooks/final-journey-planner.ipynb` for the visualization part to be running properly.


----
