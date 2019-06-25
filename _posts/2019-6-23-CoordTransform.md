---
layout: post
title: Transforming Coordinates
published: false
---

I love when someone asks me to provide them with some math help on a real-world problem. New London (CT) maker extraordinaire Mike Molinari recently sent me this message:

> Hey.
I have another math thing I am working on.
any chance you might be able to spare a few minutes and give me a hand?
It has to do with translation of 3d points.
...just need a bit of help to figure it out.

Mike explained the problem over the phone. What it boiled down to was that he:
1. was finding the coordinates of three points on a plane we'll call **T**
2. had the coordinates of points in some 3D object (e.g. a box) relative to the standard coordinate axes **i**, **j**, and **k**.
3. wanted to compute the coordinates of the points in the object relative to the coordinate axes and origin of the plane **T**.

For example, if given the points defining the cube to the lower left in the figure, and 3 points defining the plane that is graphed, the function should output the coordinates for the cube plotted in the upper right.
![coord transform img1](../images/coordTransImg1.png)

This is a nice problem for students involving concepts and techniques from linear algebra and multivariable calculus and that lends itself very readily to a computer programming implementation.  Since I was in the midst of teaching a computer programming course in Python to our sophomore Operations Research & Analytics majors, I used it as the basis of an assignment for that course.

There are a number of moving parts involved in solving this problem.  Ultimately and with input from my excellent colleauge Dana Dougherty, I decided to scaffold the assignment.  And since it is a numerical task, we used it as an opportunity to showcase an application of *NumPy*.  Next I will give an overview of the parts of the solution, describe what we scaffolded and why, and indicate where *NumPy* came in.

**Overview**

The basic idea is to covert a set of points from one coordinate system (the standard **i-j-k** one) to another coordinate system defined on the the new plane **T**.  So first, find the coordinate axes of **T**.  We are given three points in the plane **T**.  From those, we need to find two orthogonal vectors in **T** and a vector normal to **T**.  This is a straight-forward task that I assume is covered in most multivariable calculus classes (it is in ours, which the vast majority of students in this programming course have already taken).  So we leave the methodology of finding these vectors to the students, though we are ready with a couple of hints (compute a center point, how to find the second orthogonal vector in the plane) for the stuck student.  But what we do scaffold (i.e. supply to the student) are some relevant *Numpy* commands such as: 
```python
np.linalg.norm(vectAB)
np.cross(vectAB,vectAC)
```
Note that in a prior class, we had the students go through one of our in-class [worksheets on *NumPy*](https://github.com/ifrommer/course-documents/blob/master/Worksheet%20-%20numpy%20and%20matplotlib.docx).  I hope to post about these worksheets at some point soon.  They are contain a number of statements of code.  Students have to write down what they think the code will do, what it does, and whether or not their hypothesis was correct.  


...

Later I wrote back to Mike:
> I’m turning it into an assignment in my programming class for my students. As such I’ve refined the code a bit in case you want that. Also, was wondering why you needed this. Would help me motivate the lesson for the students. Thanks for the challenge.
