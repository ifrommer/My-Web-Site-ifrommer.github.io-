In earlier posts, I wrote about my experiences [visualizing](https://greenor.wordpress.com/2014/02/09/visualizing-lps-in-mathematica/) and [3D=printing](https://greenor.wordpress.com/2016/02/23/3d-printing-an-lp-feasible-region/) polytopes using *Mathematica*.
I am coming at these polytopes as feasible regions of 3-variable linear progams (LP). In a [related post about using the same "tool chain" to print solids from Calculus examples](http://ifrommer.github.io/3DPrintVolume/), I wrote:
> I find using Mathematica works well for this application but I do plan to look into how other software can be used to go from the math to the .stl file.

In particular, I was interested in an open source solution using Python, since I love using Python, and not everyone has the resources to purchase *Mathematica*.  (Side note: *Mathematica* comes installed for free on the Raspberry Pi.) One of the great aspects of PyCon 2019 were the impromptu meet-ups organized around a topic, known as "Open Spaces".  At a 3D printing Open Space, I learned from a fellow attendee about a possible pathway from LP to .stl file through Python by using **OpenSCAD** and **SolidPython**.  OpenSCAD is a free application for creation of 3D CAD objects using its scripting language.  SolidPython is a Python library for generating OpenSCAD code from Python.

To describe a polytope in OpenSCAD, you provide a list of the vertex coordinates and a list of the faces.  Each face is itself a list of the three or more vertices it contains.  Here is an example from the [OpenSCAD documentation](https://en.wikibooks.org/wiki/OpenSCAD_User_Manual/Primitive_Solids#polyhedron):

```
CubePoints = [
  [  0,  0,  0 ],  //0
  [ 10,  0,  0 ],  //1
  [ 10,  7,  0 ],  //2
  [  0,  7,  0 ],  //3
  [  0,  0,  5 ],  //4
  [ 10,  0,  5 ],  //5
  [ 10,  7,  5 ],  //6
  [  0,  7,  5 ]]; //7
  
CubeFaces = [
  [0,1,2,3],  // bottom
  [4,5,1,0],  // front
  [7,6,5,4],  // top
  [5,6,2,1],  // right
  [6,7,3,2],  // back
  [7,4,0,3]]; // left
  
polyhedron( CubePoints, CubeFaces );
```

In what follows, I outline the process in Python, SolidPython and OpenSCAD of going from an LP's constraint inequalities to the .stl file of the resulting polyhedron to be 3D printed.  The process ended up being more involved than I originally anticipated, certainly much more work than in *Mathematica*.  There is likely a simpler Python/OpenSCAD pathway, maybe via convex hulls, which I started to look into.  But the process I will describe contains some great opportunities for student assignments, and, it was fun!

Assumptions here:
1. 3-variable linear program (n=3)
2. all constraints are in less-than-or-equal-to form
Denote # of constraints *m*.

Here is an overview of the Python code I wrote:
#### The Math:
1. Input a matrix of constraints coefficiencts, one row per constraint, last column is constraint right-hand sides
2. Generate a list of all sets of basic variables - there will be n + m choose m 
3. Find the corner points
4. Create a list of the corner points associated with each face
5. Sort the corner points for each face in clockwise order (this is how OpenSCAD prefers them)
#### The 3D file creation and printing:
6. Create a polyhedron object in SolidPython using the lists of corner points and faces
7. Export the polyhedron object to an OpenSCAD file
8. In the OpenSCAD application, inspect the file and export it as .stl
9. Slice the .stl file in slicing software (I use MatterControl) and export it as a .gcode file.
10. 3D-print the .gcode file.

I was originally planning to post the code to github, but since I think this works better as an educational tool than a production one, I decided not to.  I am hapy to share the code as long as you're not one of my students ;) so if you would like it, let me know.

As an assignment in a course, there are a number of mathematical and programming challenges for students.  
Math 
- finding  the corner points (#3 above) - given the knowledge of which variables are basic, how do you find the corresonding corner point?  This is a nice exercise calling on material from the Simplex algorithm and some matrix work we cover when introducing sensitivity analysis. 
- which corner points belong to which faces? (#4 above)  how is this determined?  what is true of a face mathematically that allows one to find its corner points?
- sort corner points of a face in clockwise order (#5 above), which can be restated as given a set of points in a plane, sort them in clockwise order.  some hints: construct various vectors including a normal, I ended up using a bubble sort


Programming
- all of the math challenges above must be implemented in code
- generating the list of all sets of basic variables (#2 above) - there are many ways to implement this, the **itertools** module being a very standard one.  Since I was already using the **PuLP** module for LPs, I took advantage of its *combination* function.  A set of basic variables may or may not correspond to a basic solution, and a basic solution may or may not be feasible.  Understanding conditions for these and implementing them appropriately is necessary.
- 

scaffolding 
- usual, giving some, hiding some
- or, they must find the whole process

More about each...
4. 
...



```
# Inputs: Constraint matrix and rhs's
A0 = np.array([[3,4,2],
            [1,2,2],
            [1,1,1]])
b = np.array([[400],[300],[200]])
A = np.hstack((A0,b))
p1 = LP_Polytope(A)
print(p1)
fname = input('Enter .scad file name for export (or ENTER for no export): ')
if fname: p1.exportToScad(fname)

LP FR Polytope with instance variables:
_m = 3
_numVars = 3
_totalVars = 6
_A = [[3 4 2]
 [1 2 2]
 [1 1 1]]
_b = [[400]
 [300]
 [200]]
_Aaug = [[3. 4. 2. 1. 0. 0.]
 [1. 2. 2. 0. 1. 0.]
 [1. 1. 1. 0. 0. 1.]]
_bvSet = [(0, 1, 2), (0, 1, 3), (0, 1, 4), (0, 1, 5), (0, 2, 3), (0, 2, 4), (0, 2, 5), (0, 3, 4), (0, 3, 5), (0, 4, 5), (1, 2, 3), (1, 2, 4), (1, 2, 5), (1, 3, 4), (1, 3, 5), (1, 4, 5), (2, 3, 4), (2, 3, 5), (2, 4, 5), (3, 4, 5)]
_cpList = [{'BVs': (0, 2, 5), 'x': [50.00000000000003, 0, 124.99999999999999]}, {'BVs': (0, 4, 5), 'x': [133.33333333333331, 0, 0]}, {'BVs': (1, 2, 5), 'x': [0, 50.0, 100.0]}, {'BVs': (1, 4, 5), 'x': [0, 100.0, 0]}, {'BVs': (2, 3, 5), 'x': [0, 0, 150.0]}, {'BVs': (3, 4, 5), 'x': [0, 0, 0]}]
_faceCPs = {0: [3, 2, 4, 5], 1: [1, 0, 4, 5], 2: [1, 3, 5], 3: [1, 0, 2, 3], 4: [0, 2, 4]}


Enter .scad file name for export (or ENTER for no export): 
```

