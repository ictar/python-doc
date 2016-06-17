原文：[Computing optimal road trips on a limited budget](http://www.randalolson.com/2016/06/05/computing-optimal-road-trips-on-a-limited-budget/)

---

[![](http://www.randalolson.com/wp-
content/uploads/patreon_banner.png)](https://www.patreon.com/randal_olson)

About a year ago, I wrote an article introducing the concept of [optimizing
road trips](http://www.randalolson.com/2015/03/08/computing-the-optimal-road-
trip-across-the-u-s/) using a [combination](https://github.com/rhiever/Data-
Analysis-and-Machine-Learning-Projects/blob/master/optimal-road-
trip/Computing%20the%20optimal%20road%20trip%20across%20the%20U.S..ipynb) of
genetic algorithms and Google Maps. During that time, I've given some thought
to how I could make that algorithm more useful to folks looking to plan their
summer road trips.

One thought that struck me was that the road trips I created before were quite
grandiose--spanning [entire states](http://www.randalolson.com/2015/03/18
/pure-michigan-road-trip-optimized/) or even [most of
Europe](http://www.randalolson.com/2015/03/10/computing-the-optimal-road-trip-
across-europe/)--such that only people who had some savings and were able to
take a month off of work could even hope to go on one of the trips. In
reality, most of us have budgetary constraints on our road trips: we can only
spend so much money, or we only have so much time off before we have to get
back to work.

In this article, I'm going to expand on the idea of optimizing road trips by
introducing [multi-objective Pareto
optimization](https://en.wikipedia.org/wiki/Multi-
objective_optimization#Visualization_of_the_Pareto_front) to the algorithm.
I'll briefly describe how Pareto optimization works, and how it helps us
optimize road trips on a limited budget.

_Note: If you're not interested in the technical details of the project, skip
down to the 48 U.S. state capitols in 8 1/2 days section._

### Planning the road trip: U.S. state capitols

For this road trip, there is one goal: to take a picture at as many U.S. state
capitols as possible. (Bonus points for entertaining or themed pictures!)

We will travel only by car, so that rules out Alaska (too far away) and Hawaii
(requires a plane flight) and leaves us with the 48 contiguous states
(excluding D.C.).

Whenever possible, we will avoid routes that require us to travel through
foreign countries, as entering/leaving the country requires a passport and
border control tends to slow things down.

Lastly, to clarify: The goal is to visit the _capitol buildings_, not the city
the buildings are located in (i.e., state capitals). That said, by going on
this road trip we're in for an epic journey and some beautiful architecture.

[![vermont_state_capitol](http://www.randalolson.com/wp-content/uploads
/vermont_state_capitol-
1024x699.jpg)](https://www.flickr.com/photos/danielmennerich/5760220915/)

Image credit: [Daniel
Mennerich](https://www.flickr.com/photos/danielmennerich/5760220915/)

### Recap: Optimizing road trips

With the list of U.S. state capitols in hand, the next step is to find the
"true" distance between all of the capitols by car. Since we can't just drive
a straight line between every capitol--driving by car has this pesky
limitation of having to stay on roads--we needed to find the shortest route
_by road_ between every capitol.

If you've ever used Google Maps to get the directions between two addresses,
that's basically what we have to do here. Except this time, we need to look up
2,256 directions to get the "true" distance between all 48 state capitols--a
monumental task if we have to do it by hand. Thankfully, the [Google Maps
API](https://developers.google.com/maps/documentation/distancematrix/) makes
this information freely available, so all it takes is a short Python script to
calculate the distance and time driven for all 2,256 routes between the 48
capitols.

Now with the 2,256 capitol-capitol distances, our next step is to approach the
task as a [traveling salesman
problem](http://en.wikipedia.org/wiki/Travelling_salesman_problem): We need to
order the list of capitols such that the total distance traveled between them
is as small as possible if we visited them in order. This means finding the
route that backtracks as little as possible, which is especially difficult
when visiting Florida and the Northeast.

If you've read my [_Where's Waldo?_
article](http://www.randalolson.com/2015/02/03/heres-waldo-computing-the-
optimal-search-strategy-for-finding-waldo/), you're already aware of how
difficult it can be to solve route optimization problems like this one. With
48 landmarks to put in order, we would have to exhaustively evaluate 1.24 x
1061 possible routes to find the shortest one.

To provide some context: If you started computing this problem on your home
computer right now, you'd find the optimal route in about 3.98 x 1049 years--
long after the Sun has entered its [red giant
phase](http://en.wikipedia.org/wiki/Red_giant) and [devoured the Earth](http:/
/en.wikipedia.org/wiki/Timeline_of_the_far_future#Future_of_the_Earth). This
complication is why Google Map's route optimization service only optimizes
routes of up 10 waypoints, and the best free [route optimization
service](http://www.routexl.com/) only optimizes 20 waypoints unless you pay
them a lot of money to dedicate some bigger computers to it.

The traveling salesman problem is so notoriously difficult to solve that even
[xkcd](http://xkcd.com/399/) poked fun at it:

[![travelling_salesman_problem](http://www.randalolson.com/wp-
content/uploads/travelling_salesman_problem.png)](http://xkcd.com/399/)

Clearly, we need a smarter solution if we want to take this road trip in our
lifetime. Thankfully, the traveling salesman problem has been [well-studied](h
ttp://en.wikipedia.org/wiki/Travelling_salesman_problem#Computing_a_solution)
over the years and there are many ways for us to solve it in a reasonable
amount of time.

If we're willing to accept that we don't need the _absolute best_ route
between all of the capitols, then we can turn to smarter techniques such as
[genetic algorithms](http://en.wikipedia.org/wiki/Genetic_algorithm) to find a
solution that's good enough for our purposes. Instead of exhaustively looking
at every possible solution, genetic algorithms start with a handful of random
solutions and continually tinkers with these solutions--always trying
something slightly different from the current solutions and keeping the best
ones--until they can't find a better solution any more.

I've included a visualization of a genetic algorithm optimizing one road trip
below.

[![us-state-capitols-optimization-map](http://www.randalolson.com/wp-
content/uploads/us-state-capitols-optimization-
map.gif)](http://www.randalolson.com/wp-content/uploads/us-state-capitols-
optimization-map.gif)

### Multi-objective Pareto optimization

Normally in optimization problems, we want to maximize or minimize one
criteria: Maximize how much money we make, or minimize the chance of an
accident occurring. In multi-objective Pareto optimization, we can
_simultaneously_ optimize many criteria--for example, maximizing how many
states we visit, while at the same time minimizing the total time we spend
driving for the road trip.

In the chart below, each dot corresponds to one road trip. Watch as the
genetic algorithm simultaneously optimizes 48 road trips.

[![us-state-capitols-pareto-front-animated](http://www.randalolson.com/wp-
content/uploads/us-state-capitols-pareto-front-
animated.gif)](http://www.randalolson.com/wp-content/uploads/us-state-
capitols-pareto-front-animated.gif)

What's particularly useful about Pareto optimization is that at the end of the
optimization process, we have a _Pareto front_ to choose from that lists the
trade-offs between what we're trying to optimize. In the above chart, we see
that the more states we visit, the longer the trip will take. If we only have
2 days to take a trip, for example, the Pareto front provides a plethora of
trips to choose from: Maybe we should visit only a few capitols and have
plenty of time to explore them, or perhaps we should visit as many capitols as
possible in 2 days. The choice is ours.

In the animated map below, I've visualized all 48 of the optimized routes from
the Pareto optimization process. Notice how each route differs slightly, for
example, the optimized route that reaches 7 capitols is fairly different from
the optimized route that reaches 8 capitols.

[![us-state-capitols-animated-map](http://www.randalolson.com/wp-
content/uploads/us-state-capitols-animated-
map.gif)](http://www.randalolson.com/wp-content/uploads/us-state-capitols-
animated-map.gif)

### 48 U.S. state capitols in 8 1/2 days

After running on my laptop for about 20 minutes, the genetic algorithm reached
an optimized solution that makes a complete trip to all of the U.S. state
capitols in only 13,310 miles (21,420 km) of driving. I've mapped that route
below.

[![us-state-capitols-48-state-trip-map](http://www.randalolson.com/wp-
content/uploads/us-state-capitols-48-state-trip-map-
1024x538.png)](http://rhiever.github.io/optimized-us-capitol-road-trip/road-
trip-maps/optimized_us_capitol_trip_48_states.html)

_Click [here](http://rhiever.github.io/optimized-us-capitol-road-trip/road-
trip-maps/optimized_us_capitol_trip_48_states.html) for an interactive version
of the map_

Assuming no traffic, this road trip will take about 8 1/2 days of driving in
total, so you better bring a big water bottle. The best part is that this road
trip is designed so that you can start anywhere on the route: As long as you
follow the route from wherever you start, you'll hit every state capitol in
the 48 contiguous U.S. states, and as an added bonus, you can even add
Washington, D.C. to the route without adding any extra miles.

Here's the full list of capitols in order:

  * State House, 107 North Main Street, Concord, NH 03303
  * Maine State House, Augusta, ME 04330
  * Vermont State House, 115 State Street, Montpelier, VT 05633
  * New York State Capitol, State St. and Washington Ave, Albany, NY 12224
  * New Jersey State House, Trenton, NJ 08608
  * Pennsylvania State Capitol Building, North 3rd Street, Harrisburg, PA 17120
  * West Virginia State Capitol, Charleston, WV 25317
  * Ohio State Capitol, 1 Capitol Square, Columbus, OH 43215
  * Kentucky State Capitol Building, 700 Capitol Avenue, Frankfort, KY 40601
  * Tennessee State Capitol, 600 Charlotte Avenue, Nashville, TN 37243
  * Indiana State Capitol, Indianapolis, IN 46204
  * Michigan State Capitol, Lansing, MI 48933
  * Illinois State Capitol, Springfield, IL 62756
  * 2 E Main St, Madison, WI 53703
  * Minnesota State Capitol, St Paul, MN 55155
  * 500 E Capitol Ave, Pierre, SD 57501
  * North Dakota State Capitol, Bismarck, ND 58501
  * Montana State Capitol, 1301 E 6th Ave, Helena, MT 59601
  * Washington State Capitol Bldg, 416 Sid Snyder Ave SW, Olympia, WA 98504
  * Oregon State Capitol, 900 Court St NE, Salem, OR 97301
  * L St &amp; 10th St, Sacramento, CA 95814
  * Nevada State Capitol, Carson City, NV 89701
  * 700 W Jefferson St, Boise, ID 83720
  * Utah State Capitol, Salt Lake City, UT 84103
  * Wyoming State Capitol, Cheyenne, WY 82001
  * 200 E Colfax Ave, Denver, CO 80203
  * New Mexico State Capitol, Santa Fe, NM 87501
  * Arizona State Capitol, 1700 W Washington St, Phoenix, AZ 85007
  * Texas Capitol, 1100 Congress Avenue, Austin, TX 78701
  * Oklahoma State Capitol, Oklahoma City, OK 73105
  * 300 SW 10th Ave, Topeka, KS 66612
  * Nebraska State Capitol, 1445 K Street, Lincoln, NE 68509
  * Iowa State Capitol, 1007 E Grand Ave, Des Moines, IA 50319
  * Missouri State Capitol, Jefferson City, MO 65101
  * Arkansas State Capitol, 500 Woodlane Street, Little Rock, AR 72201
  * 400-498 N West St, Jackson, MS 39201
  * Louisiana State Capitol, Baton Rouge, LA 70802
  * 402 S Monroe St, Tallahassee, FL 32301
  * Alabama State Capitol, 600 Dexter Avenue, Montgomery, AL 36130
  * Georgia State Capitol, Atlanta, GA 30334
  * South Carolina State House, 1100 Gervais Street, Columbia, SC 29201
  * North Carolina State Capitol, Raleigh, NC 27601
  * Virginia State Capitol, Richmond, VA 23219
  * Maryland State House, 100 State Cir, Annapolis, MD 21401
  * Legislative Hall: The State Capitol, Legislative Avenue, Dover, DE 19901
  * Connecticut State Capitol, 210 Capitol Ave, Hartford, CT 06106
  * Rhode Island State House, 82 Smith Street, Providence, RI 02903
  * Massachusetts State House, Boston, MA 02108

Here's the Google Maps for the road trip: [[1]](https://www.google.com/maps/di
r/State+House,+107+North+Main+Street,+Concord,+NH+03303/Maine+State+House,+Aug
usta,+ME+04330/Vermont+State+House,+115+State+Street,+Montpelier,+VT+05633/New
+York+State+Capitol,+State+St.+and+Washington+Ave,+Albany,+NY+12224/New+Jersey
+State+House,+Trenton,+NJ+08608/Pennsylvania+State+Capitol+Building,+North+3rd
+Street,+Harrisburg,+PA+17120/West+Virginia+State+Capitol,+Charleston,+WV+2531
7/Ohio+State+Capitol,+1+Capitol+Square,+Columbus,+OH+43215/Kentucky+State+Capi
tol+Building,+700+Capitol+Avenue,+Frankfort,+KY+40601/Tennessee+State+Capitol,
+600+Charlotte+Avenue,+Nashville,+TN+37243/) [[2]](https://www.google.com/maps
/dir/Tennessee+State+Capitol,+600+Charlotte+Avenue,+Nashville,+TN+37243/Indian
a+State+Capitol,+Indianapolis,+IN+46204/Michigan+State+Capitol,+Lansing,+MI+48
933/Illinois+State+Capitol,+Springfield,+IL+62756/2+E+Main+St,+Madison,+WI+537
03/Minnesota+State+Capitol,+St+Paul,+MN+55155/500+E+Capitol+Ave,+Pierre,+SD+57
501/North+Dakota+State+Capitol,+Bismarck,+ND+58501/Montana+State+Capitol,+1301
+E+6th+Ave,+Helena,+MT+59601/Washington+State+Capitol+Bldg,+416+Sid+Snyder+Ave
+SW,+Olympia,+WA+98504/Oregon+State+Capitol,+900+Court+St+NE,+Salem,+OR+97301/
) [[3]](https://www.google.com/maps/dir/Oregon+State+Capitol,+900+Court+St+NE,
+Salem,+OR+97301/L+St+&+10th+St,+Sacramento,+CA+95814/Nevada+State+Capitol,+Ca
rson+City,+NV+89701/700+W+Jefferson+St,+Boise,+ID+83720/Utah+State+Capitol,+Sa
lt+Lake+City,+UT+84103/Wyoming+State+Capitol,+Cheyenne,+WY+82001/200+E+Colfax+
Ave,+Denver,+CO+80203/New+Mexico+State+Capitol,+Santa+Fe,+NM+87501/Arizona+Sta
te+Capitol,+1700+W+Washington+St,+Phoenix,+AZ+85007/Texas+Capitol,+1100+Congre
ss+Avenue,+Austin,+TX+78701/Oklahoma+State+Capitol,+Oklahoma+City,+OK+73105/) 
[[4]](https://www.google.com/maps/dir/Oklahoma+State+Capitol,+Oklahoma+City,+O
K+73105/300+SW+10th+Ave,+Topeka,+KS+66612/Nebraska+State+Capitol,+1445+K+Stree
t,+Lincoln,+NE+68509/Iowa+State+Capitol,+1007+E+Grand+Ave,+Des+Moines,+IA+5031
9/Missouri+State+Capitol,+Jefferson+City,+MO+65101/Arkansas+State+Capitol,+500
+Woodlane+Street,+Little+Rock,+AR+72201/400-498+N+West+St,+Jackson,+MS+39201/L
ouisiana+State+Capitol,+Baton+Rouge,+LA+70802/402+S+Monroe+St,+Tallahassee,+FL
+32301/Alabama+State+Capitol,+600+Dexter+Avenue,+Montgomery,+AL+36130/Georgia+
State+Capitol,+Atlanta,+GA+30334/) [[5]](https://www.google.com/maps/dir/Georg
ia+State+Capitol,+Atlanta,+GA+30334/South+Carolina+State+House,+1100+Gervais+S
treet,+Columbia,+SC+29201/North+Carolina+State+Capitol,+Raleigh,+NC+27601/Virg
inia+State+Capitol,+Richmond,+VA+23219/Maryland+State+House,+100+State+Cir,+An
napolis,+MD+21401/Legislative+Hall:+The+State+Capitol,+Legislative+Avenue,+Dov
er,+DE+19901/Connecticut+State+Capitol,+210+Capitol+Ave,+Hartford,+CT+06106/Rh
ode+Island+State+House,+82+Smith+Street,+Providence,+RI+02903/Massachusetts+St
ate+House,+Boston,+MA+02108/State+House,+107+North+Main+Street,+Concord,+NH+03
303/)

(Note that Google Maps itself only allows 10 waypoints to be routed at a time,
hence why there's multiple Maps links.)

### 10 U.S. state capitols in 24 hours

At this point, some of you might be scratching your heads. "Didn't you promise
to stop showing us _grandiose_ road trips, Randy?", I imagine you thinking.

This is where the Pareto optimization aspect comes in: If we look at the final
Pareto front, we don't have to pick the 48-state road trip. We have a whole
_range_ of road trips to choose from.

[![us-state-capitols-pareto-front-final](http://www.randalolson.com/wp-
content/uploads/us-state-capitols-pareto-front-final-
1024x680.png)](http://www.randalolson.com/wp-content/uploads/us-state-
capitols-pareto-front-final.png)

Let's say, for example, that we only have 24 hours to dedicate to the road
trip. If that's the case, then we can look at the Pareto front above and see
that we can reach 10 state capitols and arrive back home in less than 24
hours. That's pretty amazing to think that we can leave one morning, visit 10
state's capitols, and be back in time for breakfast the next day.

As you might expect, this road trip is in the Northeastern U.S.:

[![us-state-capitols-24-hour-trip-map](http://www.randalolson.com/wp-
content/uploads/us-state-capitols-24-hour-trip-map-
1024x728.png)](http://www.randalolson.com/wp-content/uploads/us-state-
capitols-24-hour-trip-map.png)

Here's the Google Map for the 24-hour road trip: [[1]](https://www.google.com/
maps/dir/Vermont+State+House,+115+State+St,+Montpelier,+VT+05633/New+York+Stat
e+Capitol/Capitol+Park/Maryland+State+House/Delaware+State+Capitol/125+W+State
+St,+Trenton,+NJ+08608/Connecticut+State+Capitol,+210+Capitol+Ave/Rhode+Island
+State+House/Massachusetts+State+House/State+House,+107+North+Main+Street,+Con
cord,+NH+03303/Vermont+State+House,+115+State+Street,+Montpelier,+VT+05633/dat
a=!4m68!4m67!1m5!1m1!1s0x4cb5a78ce4fcd0ef:0xf45bf416650b4910!2m2!1d-72.5804791
!2d44.262575!1m5!1m1!1s0x89de0a3aa5dc0b2b:0x72aed557f8df2510!2m2!1d-73.757339!
2d42.652835!1m5!1m1!1s0x89c8c11a73e2c79b:0x7751c1ea3a33e69a!2m2!1d-76.8829055!
2d40.263689!1m5!1m1!1s0x89b7f6502f6e80f7:0x3468f136f9ac32ce!2m2!1d-76.4909743!
2d38.9788285!1m5!1m1!1s0x89c764a9e74f4443:0xd92f2a66a2990913!2m2!1d-75.5196611
!2d39.1572775!1m5!1m1!1s0x89c159d1c586c895:0x69a1f02ee49e57ee!2m2!1d-74.769930
5!2d40.2206811!1m5!1m1!1s0x89e653689d143277:0xe1dc787b44e01dd5!2m2!1d-72.68227
38!2d41.764128!1m5!1m1!1s0x89e4451a576adb33:0x4e520f7525043b6d!2m2!1d-71.41492
43!2d41.8310955!1m5!1m1!1s0x89e3709c8355a541:0x64fc806713207cb0!2m2!1d-71.0638
101!2d42.3587772!1m5!1m1!1s0x89e2136e9b8c52cf:0x100610ccbefbf3da!2m2!1d-71.538
1058!2d43.2069292!1m5!1m1!1s0x4cb5a78ce4fcd0ef:0xf45bf416650b4910!2m2!1d-72.58
04791!2d44.262575!3e0)

If you want to look up the other road trips from the Pareto front, I've
uploaded them on [GitHub](http://rhiever.github.io/optimized-us-capitol-road-
trip/road-trip-maps/).

In theory, we can expand this idea to all kinds of budgets. If we only have
$100 to spend on gas, we can add gas costs to the Pareto front. If we can only
average $50/night on the hotel, we can add the average hotel cost at each stop
to the Pareto front. And so on. At this point, the only limit is your
imagination.

### "This is awesome! How can I optimize my own road trip?"

If you were inspired by this article and want to make your own road trip, I'm
currently in the process of cleaning up the code I used for this project. I
expect that I will have the code open sourced on GitHub by next week.

If you can't wait, I've already [published the
code](https://github.com/rhiever/Data-Analysis-and-Machine-Learning-
Projects/blob/master/optimal-road-
trip/Computing%20the%20optimal%20road%20trip%20across%20the%20U.S..ipynb) from
my previous road trip article, which will give you a good start toward
optimizing your own road trip.

You should also check out Nathan Brixius'
[solution](https://nathanbrixius.wordpress.com/2016/06/09/computing-optimal-
road-trips-using-operations-research/) to this challenge using a technique
from operations research. Nathan was kind enough to share all of his Python
code as well.

_Note: I don't make custom road trips upon request; I simply don't have the
time. However, if you have a neat road trip idea that might be interesting to
many people, please feel free to [email me your
idea](http://www.randalolson.com/contact/)._

### Conclusions

I'm reminded of the quote:

> The world is a book, and those who do not travel read only one page.

I hope that this article--through its odd mixture of travel, machine learning,
and visualization--has inspired you to go out and embark on your own road
trip. Whether it's a trip that a computer optimized in a few minutes or a trip
that took you several weeks to hand-design, it only matters that you travel
and experience the world from a fresh perspective.

Happy road tripping!
