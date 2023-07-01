# metaguessr

## problem

Time to test your GeoGuessr meta knowledge. Find the location and put the coordinates in brackets in the following way: ictf{XX.XXXXXXX,YY.YYYYYYY}

see `metaguessr.png`

## solution

here, we can see that we are on a car on a bridge near some clear waters

this clear waters are obviously the ocean, because lakes that are not connected are usually muddy or not this color of blue

we can use a few things to our advantage here

firstly, the cars are driving on the left, and not the right

we can narrow it down to those countries

then, we can use the knowledge that cars in geoguessr are specific to specific countries, and this car is the kenya car

see [geohints](https://geohints.com/)

we can use [https://geohints.com/Cars](https://geohints.com/Cars) to deduce that it is in Kenya

the coastline of kenya is very short, so we can just search the entire coastline and find it is on the kilifi bridge

the flag is `ictf{-3.6361846,39.8485131}`

