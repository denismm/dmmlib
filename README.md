# dmmlib
Library of convenient postscript functions

These are handy PS files I've built for my own use that might be useful to others.

Usage: add a soft link to dmmlib from your work directory.

```PostScript
%!
(dmmlib/base.ps) run
(dmmlib/lines.ps) run
72 softscale
4.25 5.5 translate
-3.5 dup dup neg dup 0.5 roundbox 0.75 grayfill stroke
5 thicken
7 {0 3 goto} circrepeat closepath whitefill spindlystroke
0 0 2 circle 0.5 0.25 1 rgbfill brushystroke
2 thicken
0 0 1 0 180 sparc brushystroke
0 0 goto
-0.5 -1 goto
0.5 -1 goto
0 -3 goto boltstroke
showpage
```

## base.ps
Base provides generally useful functions.

### Arithmetic and Math Operators
These are math functions that I use often enough to not want to constantly rewrite.

*int* **recip** *reciprocal*

*int int* **maximum** *max*

*int int* **minimum** *min*

*num num* **mymod** *remainder*

> This is a non-integer-requiring mod that always returns a positive number.

> `7.75 1.25 mymod` =

> **`0.25`**

*int* **randint** *int*

> Returns a random integer from 0 to int - 1.

### Dictionary Operators
*value key* **arg** *--*

> **def** with the arguments reversed.  Convenient for pulling arguments from the stack.

*key value* **defaultdef** *--*

> **def** unless the key is already defined.

### Control Operators
*int proc* **circrepeat** *--*

> Run the proc *int* times, rotating 360/*int* after each one.  
> Will rotate clockwise if *int* is negative.

*int int proc* **schlafrepeat** *--*

> Like **circrepeat**, but the second int multiplies each rotate.  
> Search for "Schl&auml;fli Symbol" for more details.

> `/star {5 2 {0 2 goto} schlafrepeat closepath stroke } def`

### File Operators

*filename l b r t* **include_eps** *--*

> Imports the named eps into the document with the lower left point at
> 0,0 and a height of 1.  This code is from the eps spec.

### Graphics State, Coordinate System, and Matrix Operators
*num* **softscale** *--*

> Scales x and y by num, and changes the linewidth to compensate.

*num* **thicken** *--*

> Multiplies the linewidth by *num*.

*num* **lighten** *--*
(**darken, mul_lighten, mul_darken**)

> Adjust the current paint color. The base forms divide the distance from
> white or black by *num* in each of r,g,b.  The mul_ forms multiply instead.

### Path Construction Operators
*x y r* **circle** --

> Appends a circle to the current path.

**lcurveto, pcurveto**

> Drop-in replacements for **curveto** that respectively draw lines to the
> control points and put dots at the control points.

*x y xrad yrad angle1 angle2* **ell**  *--*
( **elln, spell, spelln** )

> Ellipse version of **arc** and **arcn**, based on code in the Blue Book.
> The "sp" versions use the **sparc** operator from lines.ps.

*xrad yrad xrot large sweep nx ny* **svgarc** *--*
( **rsvgarc, svgsparc, rsvgsparc**)

> Ellipses based on the arc method in SVG - see SVG documentation for
> details.  The "r" prefix makes nx and ny relative to the current point.
> The "sp" versions use the **sparc** operator from lines.ps.

*l b r t* **box** *--*

> Adds a box to the current path.

*l b r t radius* **roundbox** *--*

> Adds a box with rounded corners to the current path.

*--* **curr_path** *bool*

> Indicates whether there is a current point.

*x y* **goto** *--*

> Executes a **moveto** or **lineto** as appropriate based on whether there
> is a current point.

**shavepath**

> Replaces the curent path with a new one that contains no small lines 
> (where "small" means "less than 0.1 times the currentlinewidth").

### Painting Operators
*x y* **dot** --

> Puts a small circle at x y.  Radius is 1.5 times the currentlinewidth.

-- **doot** --

> Puts a dot at 0,0.

*--* **whitefill** *--*

*num* **grayfill** *--*

*red green blue* **rgbfill** *--*

> Fill the current path with the given color, leaving the path unaffected.

*l b r t spacing* **dotgrid** *--*

> Draw a grid of dots in the given box with the given spacing.

### Glyph and Font Operators

*string* **centershow** *--*

> Renders the string centered on 0,0.  Uses pathbbox, so descenders will
> make the text show up higher.


## lines.ps
## textbase.ps
## object.ps
## readpnm.ps
## polar.ps
## engine.ps
