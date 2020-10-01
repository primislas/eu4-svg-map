## Rasterize SVG

Generally speaking, produced map turned out to be too big to be converted to an image 
by existing online converters. Furthermore, if you build an svg with names, none of those
services will be able to preserve names in resulting image. So the best approach is to 
open the map in Chrome or Firefox and capture the page as an image.
* Chrome has it hidden:
    1. open the map
    1. _Ctrl + Shift + I_ to open dev tools
    1. _Ctrl + Shift + P_ to open dev console
    1. type in _screenshot_ and select "Capture full size screenshot"
* In Firefox:
    1. open the map
    1. _Ctrl + Shift + K_ to open dev console
    1. type in _:screenshot --fullpage_
    1. alternatively copy to clipboard with _:screenshot --fullpage --clipboard_
    
Additionally, you could use chrome (and firefox) from command line to do the conversion. However, be careful.
I noticed that chrome could produce weird looking texts in headless mode.
> "C:/Program Files (x86)/Google/Chrome/Application/chrome" --headless --window-size=5632,2048 --screenshot="D:/eu4_political.png" "file:///D:/eu4_political.svg" 
