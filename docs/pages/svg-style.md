## SVG Styles

* [General](#general)
* [Useful Tips](#useful-style-configs)
* [Provinces](#provinces)
* [Borders](#borders)
* [Rivers](#rivers)
* [Names](#names)
* [Backgrounds](#backgrounds)

### General

* Svg styles are almost identical to general css styles.
* As a consequence of the above, various hover and transform effects are also possible.
* Style configuration can be found at the top of generated svg.
* There're individual classes for provinces, borders, rivers, names, backgrounds.
* Note that you can edit styles live in chrome. Right click the element you're interested in
(such as a province or a name), select "Inspect element" and modify styles in the opened window.

### Useful style configs

* You can make an element click-through-able (**transparent for mouse pointer**). For example,
if you don't want rivers and/or names to obstruct province tooltip. Add
_**pointer-events: none;**_ to the class you're interested in (.tn or .river).
* Alternatively, if you have a transparent element (for example if you made an uncolonized province
or wasteland transparent with fill: none) selectable / visible for pointer, apply
_**pointer-events: bounding-box;**_ style to it.
* Remember that various svg widths don't have to be integers. I.e. you could have a decimal stroke width.

### Provinces

* Oikoumene distinguishes between the following province types (map classes): city, uncolonized, wasteland,
sea, lake.

### Borders

* There's no way to add two colors to the same border line in SVG to build two borders matching
country colors as in EU4. Therefore, there's only a single border line between every 2 adjacent
provinces.
* All border classes are prefixed with .border: .border-land, .border-land-area, .border-country, etc,
see the svg.

### Rivers

* All river classes are prefixed with .river: narrowest, narrow, wide, widest.

### Names

* Names are configured with .tn and .tn-tiny classes ("tag name"), where .tn-tiny is used for
texts with a font small enough to look "bad" with an outline. It was a subjective judgement call.
* We're using Google's Dosis font. Fonts can be easily imported, just use the import directive in style tag:
```
@import url("https://fonts.googleapis.com/css2?family=Dosis:wght@800");
```
* Add _pointer-events: none;_ style to text if you want it to be transparent for mouse. We've left
names as is so that they were easy to select and inspect.

### Backgrounds

* We're setting backgrounds with a pattern element that is then used as a fill on a polygon.
```
  <defs>
    <pattern id="background-water" patternUnits="userSpaceOnUse" width="5632" height="2048">
      <image x="0" y="0" width="5632" height="2048" xlink:href="https://raw.githubusercontent.com/primislas/eu4-svg-map/master/resources/colormap-water.png"></image>
    </pattern>
    <pattern id="background-terrain" patternUnits="userSpaceOnUse" width="5632" height="2048">
      <image x="0" y="0" width="5632" height="2048" xlink:href="https://raw.githubusercontent.com/primislas/eu4-svg-map/master/resources/colormap-summer-nowater.png"></image>
    </pattern>
  </defs>
  <polygon class="terrain" fill="url(#background-water)"  points="0,0 5632,0 5632,2048 0,2048"></polygon>
  <polygon class="terrain" fill="url(#background-terrain)"  points="0,0 5632,0 5632,2048 0,2048"></polygon>
```
* To load images with a url use _xlink:href="https://..."_ attribute
* To load a file use _xlink:href="file://D:\eu4\..."_ attribute
* To make an image stretch to the polygon size, use preserveAspectRatio="none" on the image:
```
<image x="0" y="0" width="5632" height="2304" preserveAspectRatio="none" xlink:href="file://D:\....png"></image>
``` 
* See [tutorial on patterns](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Patterns) 
for more information.
* Note that eu4 backgrounds are stored in .dds format. For base game and MEIOU we manually converted them to .png 
and cut out water from terrain season maps, then putting them over water map. Unmodded maps can use our
pngs for their backgrounds (which are in fact inserted automatically when generated with oikoumene).
* Alternatively, you're free to use any custom map or even texture.
