## Name Placement

### Table of Contents

* [Requirements](#requirements)
* [Paradox Algorithm](#paradox-algorithm)
* [Oikoumene Algorithm](#oikoumene-algorithm)
  * [Summary](#notes-on-algorithm)
  * [Implementation](#oikoumenes-implementation)
  * [Deficiencies](#deficiencies)
  * [Further enhancements and alternatives](#further-enhancements-and-alternatives)


### Requirements

* Names lie on a parabolic path. A good reason for limiting
  curvature to parabola is that names lying on more complex curves
  simply aren't very readable. Additionally, parabolas suffice for
  the vast majority of names to look nice. Finally, parabolic
  fitting is relatively quick and inexpensive resource-wise.
* Names should be placed geometrically centrally. The practical
  consequence is that you can't use, say, province centers to approximate
  the name curve, as provinces are not evenly distributed. It is
  mandatory to identify the general border of a shape that you are naming.
  Since countries are groups of provinces, it means that you need
  to identify country shape / country borders for name placement.
* Clausewitz allows names to "leave" the shape and be written over
  wastelands and water, but not other countries or uncolonized provinces.

### Paradox Algorithm

A reddit [post](https://www.reddit.com/r/paradoxplaza/comments/2jcf2x/comment/clb2rsr/)
from PDox game engine dev described their algorithm in 2014.
Quoting in case the original post goes down.

> The way we do it (in general, it differs a bit from game to game):
> 
> 1. First categorize each province into separate connected territory
>   (so we can generate one name for Americas and one for Europe for example)
> 2. Add all pixels for all provinces in a territory to a list
>    (you can save some time by adding every fourth pixel or so if you wish)
> 3. Do two linear least squares fitting passes (http://mathworld.wolfram.com/LeastSquaresFitting.html),
>    the second one with x/y swapped to get two lines in the form y = kx + m and x = ky + m
>    (different k and m resp. ofc)
> 4. Create some extra lines parallel to these two, with some offset distance
> 5. Ray-trace each line in the map to find the longest line segment possible
>    (lines are invalid on water and enemy territory)
> 6. Once we find the longest line segment, place three offsets along the line
> 7. For each offset, make a new line perpendicular to the line segment
>    and ray-trace in the map again to find the full extent of this new line segment
> 8. Take the mid-point of the three new line segments and use as verts for a cardinal spline.
>    In order to get enough verts for the spline, extrapolate some new ones from the three you got
> 9. Place characters along the cardinal spline
> 
> That's it really. Of course there are both gameplay tweaks to consider
> (maybe water is ok sometimes, maybe some close provinces could be considered
> connected even though they aren't) and some other tweaks to make it look better.
> 
> It doesn't always work, but the algorithm in use is still mostly the same
> as the one in Victoria II, and that was developed in a really short time.
> One day we will return to it to make improvements, but in the meantime
> this one does a decent job.

### Oikoumene Algorithm

#### Notes on algorithm

* Polynomial curve fitting for a degree of 2 does the tricks,
  most languages should have libraries readily available for it.
* Note that curve fitting algorithms (least squares in most cases)
  work along provided x and y axes. Which means that we can only get
  a classical or an upside down parabola. These can't represent
  "vertical" names very well.
* As a consequence of the above, we should first identify shape's
  spatial orientation - the line or the angle that will serve
  as the x-axis for our fitting.
* Since we're looking for a geometrically central nameline, it
  seems reasonable to collect points, that are central vertically
  with a certain horizontal step. Calculate them along the line / axis
  identified in the previous step.
* Apply fitting algorithm to collected points. The line is ready.
* The next important step is identifying font size and placement
  depending on available space. This is highly dependent on available space.
  For smaller shapes where space is tight font pixelsize might become
  significant in relation to available pixel length. There're no
  general algorithms here, fit according to requirements, style guides, etc.
* A few general notes:
    * usually it becomes necessary to reduce font size for larger names
    * short names (up to 5 letters) normally don't look well when letters
      are too far apart
    * there's a peculiar case of "long thin shapes", where available
      "long" line might prompt you to assign font size that in reality
      is too big, because the shape is "thin"; these cases should be accounted for
    * if you're doing weighted curve fitting (and you should!), weigh
      "thinner" sections more, that will make your names "dive" into
      thin areas better

#### Oikoumene's implementation

1. Identify groups of neighboring provinces belonging to the same tag.
   For each province group do the following.
2. Identify common shape border. Since province tracer gives us
   individual border segments, we can assemble single country border
   out of appropriate segments.
3. If we were to take all the border points for fitting, we would get
   results that can be skewed and hijacked by complex and ridged borders,
   because these tend to have a lot more points. Additionally, this approach
   would be susceptible to various otherwise insignificant border curves.
   For example, consider the English South-West.

   ![english south west](../images/england-south-west.png)
4. To account for all of that, we build an approximate convex
   outer border representation with more or less evenly spaced out
   points. Specifically, for each (step = 5) x we'll put two points
   that represent min and max border points in this range.
   We'll put one (average) point at left and right (by x) edges.
   We'll do the same for y. The result is not strictly speaking optimal,
   but it's a quick and simple algorithm that yields sufficiently good results.
5. Now we can identify orientation based off produced approximate border.
   In practice, we don't need an exact angle for good fitting. Significant
   deviations are allowed. Perhaps even up to 30 degree range. Having considered this,
   we propose to identify orientation by drawing lines through the shape's
   centroid with a 15 degree step starting from 0 and ending at 165 degress.
   Best fitting orientation is the line that is closest to all the border points.
6. Now we can build an approximate name polyline respective to
   identified orientation line. It is a collection of points that
   are y average for each x section (oikoumene uses a step of 5).
   Remember to weigh them inversely respective to shape thickness
   at a current step. The thinner the shape, the more weight.
   That way names would curve towards thin sections better,
   which is generally what we want.
7. Remember to track general thickness to later account for
   "long thin shapes" (cases like Orissa).

   ![long-thing-fit](../images/long-thin-fit.png)
8. The polyline can be approximated with any
   polynomial curve fitting implementation.

The image below illustrates all the steps: approximated border,
centroid, orientation line, name polyline, name parabola, name.

![mamluk-name-fitting](../images/name-fitting-mamluks.png)

#### Deficiencies

The algorithm has reached its limit in its current oikoumene
implementation. It has a number of deficiencies that require
qualitative improvements rather the tweaking at the edges,
as the latter now usually improves placement for certain shapes
while making it worse for others.

1. It is necessary to discard insignificant sections. E.g. oftentimes
   "thin" sections at border edges are simply a result of there being
   too few pixels, but they can skew name placement considerably,
   especially for smaller shapes.
2. The algorithm doesn't handle very complex and tentacled shapes well.
   Bordergore = namegore.
3. The algorithm in its raw form doesn't prevent names from going
   over other countries. Blocking this, while allowing water and wastelands
   requires a qualitative algorithm improvement.
4. The algorithm in its raw form can't handle concavity at shape's
   left and right edges. For example, Ajam or in the most extreme case
   Yarkand. Learning to identify and deal with these cases
   is the next qualitative step of algorithm enhancement.

   ![edge-concavity](../images/edge-concavity-fit.png)
5. Horseshoe shapes do not always produce a result you'd expect.
   Identifying these kinds of shapes could be an aesthetic microtweak.
   Consider Nupe, Benin and the aforementioned Yarkand. It seems
   reasonable to expect a horseshoe-like name for these, no?
   Could be just me. :)

   ![horseshoe-names](../images/horseshoe-names.png)

#### Further enhancements and alternatives

One major alternative to be considered is fitting not to
a parabola but to an ellipse. An ellipse would account for quite
a few of the deficiencies mentioned above. However, it could also
be an impractically resource-hungry algorithm.

A major improvement would be name tracing to detect whether
it leaves a tag boundary (with a few exceptions like water and wastelands).
Name sections beyond the boundary can be truncated, and then
the longest "within bounds" name could be selected,
as in Paradox solution.
