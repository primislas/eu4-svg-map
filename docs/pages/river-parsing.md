## Rivers

River modding section on eu4 wiki sufficiently describes river configs.
The important take-aways for parsing purposes are:
* Rivers can consist of multiple branches and segments:
they can merge, split, have different width configured for the same river
along its flow.
* Oikoumene's river tracer represent rivers as a group of segments,
where a segment is a group of pixels belonging to the same river
and having the same width.
* Each individual river section in rivers.bmp is guaranteed to have
a "starting" point, from which we can begin tracing. Be it a river
source, flow-in or flow-out.
    1. On the first bmp pass identify these starting points.
    1. Trace sections that start from sources. If you're tracking direction,
    these flow along the line of tracing. Mark pixels as already traced,
    you'll need it later for tributary tracing.
    1. Then you can trace flow-ins and flow-outs. As there're at least 2
    river sections adjacent to 'tributary start' pixel, this is where
    you identify which one to take by discarding already traced pixels from
    the previous step. 
    1. Note that for flow-ins you'll have to reverse final direction.
    (Depending on rendering engine, reversing river pixel list could suffice.)
    1. Note that flow-outs could have multiple tributaries start from
    the same source pixel.
    1. Don't forget to connect your tributaries / flow-outs to
    the main river.
* Further, it seems that Clausewitz can dynamically identify
"river crossing" types of borders from the map. This has not yet
been investigated by the oikoumene team.
* Further, it seems that Clausewitz can dynamically identify
estuaries? Also not yet investigated.
* As with borders, Clausewitz applies some sort of polyline smoothing
curve conversion to rivers.
