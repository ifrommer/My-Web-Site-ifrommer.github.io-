---
layout: post
title: 3D Printing a Volume by Cross-Section Problem
---

<p class="Text">In Calculus II, during the volume by slicing section, we had the following problem:</p>
<span style="font-style:italic;">Use the general slicing method to find the volume of the solid with a semicircular base of radius 16 whose cross sections perpendicular to the base and parallel to the diameter are squares.  Place the​ semi-circle on the​ xy-plane so that its diameter is on the​ x-axis and it is centered on the​ y-axis. Set up the integral that gives the volume of the solid. Use increasing limits of integration. </span>

source:  Briggs, W. L., Cochran, L., & Gillett, B. (2014). Calculus for scientists and engineers: Early transcendentals. Pearson Education.

One student came up with a different answer from the correct one. The reason was that while he had oriented his square cross sections “perpendicular to the base”, as the problem instructs, he had them arrayed perpendicular to the diameter of the semi-circular base, not parallel to it as the problem instructs. We talked about the difference in class and I made some sketches on the board.  It seemed like students sort of understood the difference.  But I wanted to make it clearer.  So that evening I 3D printed the two volumes, each in two pieces so that we could hold the results in our hands and see how the cross-sections were oriented.

My general process for 3D printing mathematical objects is as follows:

In <em>Mathematica</em>:
1. Define the set of points constituting the shape
2. Generate a 3D plot of this set using the command RegionPlot3D
3. Export the 3D plot to .stl format

In <em>MatterControl </em>(any other slicing software should do):
4. Import the .stl file
5. Adjust settings as necessary – this could include infill amount, z layer height, resizing it, rotating, etc.
6. Export as a .gcode file

7. Print on my <em>Anet A2</em> 3D printer

I find using <em>Mathematica </em>works well for this application but I do plan to look into how other software can be used to go from the math to the .stl file.

Below is the <em>Mathematica </em>used for this problem (the correct way followed by the perpendicular-to-diameter way).

<h3>Correct Way</h3>
$side = x^2 + y^2 \le  16^2 \;\; \textrm{&&} \;\; y \ge 0;$

$bottom = z \ge 0;$

$top = z \le \sqrt{16^2 - y^2}$

$all = side  \;\;\; \textrm{&&} \;\; bottom \;\;\; \textrm{&&} \;\; top;$

Here I split the solid into 2 parts for printing so we can better see the cross sections. I chose to do so at y=6.

$ySplitLoc = 6;$

$partA = all  \;\; \textrm{&&} \;\;  y ≤ ySplitLoc;$

$partB = all  \;\; \textrm{&&} \;\;  y ≥ ySplitLoc;$

$parts = {partA, partB}; names = {"A", "B"};$

*correctSolidParts = Table[RegionPlot3D[part, {x, -20, 20}, {y, 0, 20}, {z, 0, 40}, BoxRatios -> Automatic, PlotPoints -> 100], {part, parts}]*

This creates the two sub-plots in <em>Mathematica</em>:
<!-- Couldn't figure out how to get relative reference working in jeckyl/github -->

<table width="500px" height="100%" border="1">
<tr>
<td>
<img src="http://ifrommer.github.io/images/correctA.png" align = "left" alt="Correct One" HSPACE="50" VSPACE="10" width="216" height="306">
</td>
<td>
<img src="http://ifrommer.github.io/images/correctB.png" align = "right" alt="Correct way - part B" HSPACE="50" VSPACE="10" width="238" height="311">
</td>
</tr>
</table>

<h3>Perpendicular-to-Diameter Way</h3>
Use the same bottom and side as before.

$top = z \le \sqrt{16^2 - y^2}$

*all = side && bottom && top;*

Using a similar process as above, I plotted the region split into two parts:

[caption id="attachment_146" align="alignright" width="309"]<img class="alignnone  wp-image-146" src="https://ifrommer.files.wordpress.com/2019/01/perpDiamA.png" alt="perpDiamA" width="309" height="211" /> Perpendicular to diameter - part B[/caption]

[caption id="attachment_149" align="alignleft" width="322"]<img class="alignnone  wp-image-149" src="https://ifrommer.files.wordpress.com/2019/01/perpDiamB.png" alt="perpDiamB" width="322" height="212" /> Perpendicular to diameter - part A[/caption]

 

 

 

 

 

 

 

 

Here is what it looked like while printing (perpendicular to diameter one):

 

[caption id="attachment_150" align="aligncenter" width="309"]<img class="alignnone  wp-image-150" src="https://ifrommer.files.wordpress.com/2019/01/printing_b2rotd.jpg" alt="printing_b2(rotd)" width="309" height="316" /> Printing![/caption]

https://youtu.be/vVm8YCj1ho4

Finally, here is a video showing the printed shapes and how one can manipulate them to get a good feel for the cross sections:

https://youtu.be/y4aae6l96TA
