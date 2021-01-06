# dmmlib
Library of convenient postscript functions

These are handy PS files I've built for my own use that might be useful to others.

Usage: add a soft link to dmmlib from your work directory.

```PostScript
%!
(dmmlib/base.ps) run
(dmmlib/lines.ps) run
(dmmlib/fill.ps) run
72 softscale
4.25 5.5 translate
-3.5 dup dup neg dup 0.5 roundbox 0.75 grayfill stroke
5 thicken
7 {0 3 goto} circrepeat closepath 0 0 0.5 100 gradientfill spindlystroke
0 0 2 circle 1 0.25 0.5 rgbfill brushystroke
1 1 dot
-1 1 dot
2 thicken
0 0 1.25 0 180 sparcn brushystroke
2.5 3 goto
1 0 4 0 2.5 -3 curveto boltstroke
showpage
```

## base.ps
Base provides generally useful functions.

### Arithmetic and Math Operators
These are math functions that I use often enough to not want to constantly rewrite.

*int* **recip** *reciprocal*

*int int* **maximum** *max*

*int int* **minimum** *min*

*int* **half** *int*

*num num* **mymod** *remainder*

> This is a non-integer-requiring mod that always returns a positive number.

> `7.75 1.25 mymod` =

> **`0.25`**

*int* **randint** *int*

> Returns a random integer from 0 to int - 1.

*min* *max* *resolution* **randrange** *num*

> Returns a random number between **min** and **max**, at a resolution of 1/**resolution** per 1.  No argument needs to be an int.

> `-4 4 100 randrange` =

> **`-2.05`**

### Dictionary Operators
*value key* **arg** *-*

> **def** with the arguments reversed.  Convenient for pulling arguments from the stack.

*key value* **defaultdef** *-*

> **def** unless the key is already defined.

### Control Operators
*int proc* **circrepeat** *-*

> Run the proc *int* times, rotating 360/*int* after each one.  
> Will rotate clockwise if *int* is negative.

*int int proc* **schlafrepeat** *-*

> Like **circrepeat**, but the second int multiplies each rotate.  
> Search for "Schl&auml;fli Symbol" for more details.

> `/star {5 2 {0 2 goto} schlafrepeat closepath stroke } def`

*proc* **hmirror** *-*

*proc* **vmirror** *-*

> Takes a proc and runs it twice, once with a `-1 1 scale` or `1 -1 scale` in effect.

### File Operators

*filename l b r t* **include_eps** *-*

> Imports the named eps into the document with the lower left point at
> 0,0 and a height of 1.  This code is from the eps spec.

### Graphics State, Coordinate System, and Matrix Operators
*num* **softscale** *-*

> Scales x and y by num, and changes the linewidth to compensate.

*num* **thicken** *-*

> Multiplies the linewidth by *num*.

*num* **lighten** *-*
(**darken, mul_lighten, mul_darken**)

> Adjust the current paint color. The base forms divide the distance from
> white or black by *num* in each of r,g,b.  The mul_ forms multiply instead.

### Path Construction Operators
*x y r* **circle** -

> Appends a circle to the current path.

*x y r* **square** -

> Appends a square to the current path centered at xy with a side of 2r.

**lcurveto, pcurveto**

> Drop-in replacements for **curveto** that respectively draw lines to the
> control points and put dots at the control points.

*x y xrad yrad angle1 angle2* **ell**  *-*
( **elln, spell, spelln** )

> Ellipse version of **arc** and **arcn**, based on code in the Blue Book.
> The "sp" versions use the **sparc** operator from lines.ps and require
> importing that file.

*xrad yrad xrot large sweep nx ny* **svgarc** *-*
( **rsvgarc, svgsparc, rsvgsparc**)

> Ellipses based on the arc method in SVG - see SVG documentation for
> details.  The "r" prefix makes nx and ny relative to the current point.
> The "sp" versions use the **sparc** operator from lines.ps and require
> importing that file.

*l b r t* **box** *-*

> Adds a box to the current path.

*l b r t radius* **roundbox** *-*

> Adds a box with rounded corners to the current path.

*-* **curr_path** *bool*

> Indicates whether there is a current point.

*x y* **goto** *-*

> Executes a **moveto** or **lineto** as appropriate based on whether there
> is a current point.

**shavepath**

> Replaces the current path with a new one that contains no small lines 
> (where "small" means "less than 0.1 times the currentlinewidth").

### Painting Operators
*x y* **dot** -

> Puts a small circle at x y.  Radius is 1.5 times the currentlinewidth.

*-* **doot** -

> Puts a dot at 0,0.

*-* **whitefill** *-*

*num* **grayfill** *-*

*red green blue* **rgbfill** *-*

> Fill the current path with the given color, leaving the path unaffected.

*l b r t spacing* **dotgrid** *-*

> Draw a grid of dots in the given box with the given spacing.

### Glyph and Font Operators

*string* **centershow** *-*

> Renders the string centered on 0,0.  Uses pathbbox, so descenders will
> make the text show up higher.


## lines.ps
A library for drawing lines differently.  More information can be found at
[Alternate line styles in Postscript](http://suberic.net/~dmm/graphics/lines/lines.html).

*proc* **var_line** *-*

> Replaces the current path, which can't contain internal **moveto** operators,
> with an outline of a variable-width stroke of that path.
> Respects linecap, linejoin, and miterlimit settings.
> The proc must return half of the desired line thickness at every point 
> (any point mentioned in
> **moveto** or **lineto** after **flattenpath** is called to remove curves) 
> based on internal variables in **var_line**.  Useful variables:

*   i: position along the array of points
*   pathcount: length of the array of points
*   dist: current position along the length of the path
*   linelen: length of the entire path

**bolt, boltstroke**

*num* **boltoutline** *-*

> Replacement for **stroke** that calls **var_line** with a function that
> smoothly decreases linewidth to 0 along the line length.
> **bolt** simply replaces
> the current path with an outline of the new path.  **boltstroke** fills
> that path, making it a full replacement for stroke.  **boltoutline**
> strokes the outline and takes an argument which is the thickness of the
> outline.  The linewidth setting is not changed as a result of the call.

**bipoint, bipointstroke, bipointoutline**

> Replacement for **stroke** that calls **var_line** with a function that
> sets the linewidth to 0 at the start and end, leaving it alone otherwise.
> The three operators follow the same pattern as **bolt** and family.

*spline_offset* **calligraphic** *-*

> Replaces the current path, which can't contain internal **moveto** operators,
> with an outline of a "calligraphic" stroke of that path.  This path
> goes to full width at the endpoints of a **moveto**, **lineto**, or 
> **curveto** operator, but gets thinner in between those points (including
> along the path of a curve).  The argument determines how thick the
> thin segments are, in a not easily describable way - 0 will make a straight
> line get as thin as 1/4 the base thickness, 1 will look like a normal stroke
> for straight lines.  See the lines.html web page for more details.

*spline_offset* **callistroke** *-*

*num spline_offset* **callistroke** *-*

> **callistroke** fills the calligraphic outline, **callistroke** strokes it
> as in the **bolt** family above.

**spindly, spindlystroke, spindlyoutline**

**brushy, brushystroke, brushyoutline**

> Call **calligraphic** and family with arguments of 0 and 1/3 respectively,
> creating lines of nominal 1/4 and 1/2 minimum thickness.  **brushystroke**
> is the operator I use most often from this library.

**cstroke**

> Defined to be equivalent to brushystroke but see next three methods.

**setplain**

> Define **cstroke** to be equivalent to **stroke**.

**setbrushy**

> Define **cstroke** to be equivalent to **brushystroke**.

**setspindly**

> Define **cstroke** to be equivalent to **spindlystroke**.

**sparc, sparcn**

> Drop-in replacements for **arc** and **arcn** that draw a single spline for
> the entire arc as opposed to breaking it every 90 degrees.  The curve
> approximation is unnoticeable from 0 to 90 degrees, becomes slightly square
> by 180 degrees, and quickly becomes obviously distorted above 180.

*x y rad* **spcircle** *-*

> Adds a circle to the path, like **circle** in base.ps, using sparc to draw
> semicircles from 0 to 180 and from 180 to 360.

*num proc* **ObjectLine** *-*

> Strokes the current path by calling *proc* at the 0,0 point and then every
> *num* units along it. *proc* should draw something centered at 0,0.  It
> will be rotated to match the line, as if the line was traveling from left
> to right.

*x<sub>1</sub> y<sub>1</sub> x<sub>2</sub> y<sub>2</sub> x<sub>3</sub> y<sub>3</sub> divisions* **splitcurveto** *-*

> As **curveto** but creates *divisions* sub-splines, following the same
> path as the spline *curveto* would create.  Undetectable with a normal
> stroke but possibly useful when using some of the line styles in this
> library.

## textbase.ps
Text manipulation and display functions.

*string string* **concatenate** *string*

> Concatenate two strings.  Not to be confused with the built-in operator
> **concat** which combines matrices.

**X_center, x_center, Xj_center**

> **Translate** so as to put y=0 in the vertical center of future text 
> in the current font.  The center is defined as the middle of X, x, or Xj
> appropriately (capital, minuscule, capital plus descender).

*string* **left_base_show** *-*

*string* **right_base_show** *-*

*string* **center_base_show** *-*

> **Show** the string with a baseline at y=0.  The string will have its
> left, right, or center at x=0.

*string num proc* **width_line_show** *-*

> Call the proc (which should be one of the **_base_show** family above or
> similar) to show the string, using scale to compress the line horizontally
> if necessary to keep it within the given width.

*string linewidth lineheight proc* **paragraph_show** *lines*

> **Show** the string formatted into a paragraph.  Wrap lines to
> linewidth when possible.  Compress long words horizontally to
> fit in the linewidth.  Drop lineheight units between lines.
> Respect newlines.  Return the number of lines shown.  Does not
> change the current transformation matrix.

*string radius* **circle_center_show** *-*

> **Show** the string along a circular path, centered on 
> the origin, with the X center at the specified radius.
> The string will be centered around the 90-degree axis.  If a
> negative radius is provided the text will curve inward and be centered
> on the 270-degree axis.

*basefont encoding newfont* **change_encoding** *-*

> Define a new version of *basefont* with the given encoding.

> `/Times ISOLatin1Encoding /Times-ISO change_encoding`

## fill.ps
Replacements for fill.  This file runs base.ps and textbase.ps.

*xseparation yseparation proc* **ObjectFill** *-*

> Fill the current path with copies of the object rendered by *proc*.  
> Objects will be arranged in a grid with a point at 0,0.  If a point
> in the grid is inside the path, it will be drawn - there is no clipping,
> so if an object is near the edge it will extend outside the original path.

*xseparation yseparation maxdepth proc* **FractalFill** *-*

> Like **ObjectFill** but if a 2x2 set of points is inside the path, they'll
> be replaced by a twice-as-large version.  A 2x2 set of the larger versions
> will also be replaced by a yet-larger version, for *maxdepth* iterations.
> The larger versions are made using **softscale** so the line width will
> not change between scales.

*red green blue steps* **gradientfill** *-*

> Fill the current path with a gradient starting at red green blue at the 
> edge and becoming white in the center.

## readpnm.ps
Load a PPM or PGM format image onto the page.  For more details, see
[Read PNM files from postscript code](http://suberic.net/~dmm/graphics/readpnm/readpnm.html).

*filename* **drawpnm_width** *-* (**drawpnm_height, drawpnm_square, drawpnm_height**)

> Draw the ppm or pgm in filename onto the page.  The image will have its 
> lower corner at 0,0.  The end of the operator indicates how to handle the
> size of the image.

* **_width**: scale so width is 1 unit
* **_height**: scale so width is 1 unit
* **_square**: scale so smallest dimension is 1 unit, clip to 0,0,1,1 square
* **_fit**: scale so largest dimension is 1 unit, center in 0,0,1,1 square

## polar.ps
## engine.ps
