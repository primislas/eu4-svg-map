## SVG Rendering

Breakdown of svg-related issues and nuances.

First and foremost, there's still no uniform processing of textPath
attribute. It means that **browsers render textPath texts differently**.
Further, various editors relying on librsvg can't handle curved
texts at all. So our options are limited to Chrome and Firefox.

Clausewitz renders **country borders** by rendering separate
borders for each country, which are essentially stripes, inner
respective to general country border. We've found no easy way
to reproduce it with svg.

Further, lion's share of svg size is comprised of border polyline
descriptions. Duplicating them for the sake of making fancier country
borders is hardly worth it.

If you're dealing with **nested (inner, enclosed) shapes**, you
either have to preserve shape rendering order throughout all
transformations, which severely limits ability to group provinces
together, or (preferred method) you could clip enclosed shapes
from outer ones. This can be achieved with *fill-rule="evenodd"* 
setting.

Remember, for svg various font-sizes, and stroke-widths
don't have to be integers!
