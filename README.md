# Route Permutation Generator
Add new folder named <i>"RPG_Files"</i> to the same folder as file <i>"run_route_permutation_generator.php"</i>.<br>
Run: php -f run_route_permutation_generator.php<br>
# 
<p>
This script generates all possible route permutations between 2 stops, given datasets of routes from different modes of transport (e.g. MyCiti Bus Routes and Metrorail Train Routes) in order to find the shortest or quickest route between 2 GPS locations.
<br>
<i>(MOT == Mode of Transport).</i>
</p>
<p>This is achieved in the following way:</p>
From File: “stop_route_proximity_table.php”:
<ol>
  <li>The distance between All Stops on all Routes from all MOTs are measured using GPS co-ordinates and are entered into the database as relative Stops if the distance is less or equal to the minimum walking distance set in the “MAX_WALKING_DIST” variable.</li>
</ol>
<br>
From File: “route_permutation_generator.php”, Class: “Route_Permutation_Stack”:
<ol>
    <li>All combinations of MOTs, without duplicates, are generated.</li>
    <li>For each MOT combination; All Stops, from all Routes, for each MOT are collected into Start and Destination pairs.</li>
    <li>For each Start and Destination pair, an instance of the class “Route_Permutation” is run in it’s own process.</li>
</ol>
<br>
From File: “route_permutation.php”, Class: “Route_Permutation”:
<ol>
    <li>A search is started on the Route of the “Start” Stop (I get the irony) and at the “Start” Stop.</li>
    <li>For each Stop, A list of Stops in range of that Stop (generated by “stop_route_proximity_table.php”) is retrieved from the database.</li>
    <li>For each Stop in proximity, a new instance of Class: “Route_Permutation” is run in it’s own process with the Stop in proximity as the “Start” Stop and the Destination set to the same Destination Stop as the current route permutation. The history of the Current Route Permutation is also passed on to new Route Permutations as to prevent new route permutations doubling back on themselves.</li>
    <li>The current Route Permutation will then proceed to the next Stop on it’s route and perfrom step 2 and 3.</li>
    <li>If the Destination Stop is reached by the end of the Route Permutation’s investigation, the Route Permutation is saved to the database with the Start and Destination Stops as the index.</li>
</ol>
<p>
The Route_Permutation_Stack class controls all instances of the Route_Permutation class (i.e. When to run, Updates to relevant variables and when to kill the instance) and comunicates via server sockets.
</p>
