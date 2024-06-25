---
layout:      project  # Must be set to project
date:        2 Jan 2017
title:       Mesh Editing & Texture Mapping
caption:     An exploration of mesh editing, upsampling, and texture mapping.
image:
  path:      /assets/thumb/cs184-2.webp
  srcset:
    700w:    /assets/thumb/cs184-2.webp
    480w:    /assets/thumb/cs184-2@480w.webp
    240w:    /assets/thumb/cs184-2@240w.webp
description: >
  A pikachu texture mapped onto a cow mesh, with a rainbow shader.
# links:
#   - title:   Demo
#     url:     /assets/pdf/pdf.pdf
featured:    false
sitemap:     true
---

# Mesh Editing & Texture Mapping

## Part 1: Fun with Bezier Patches
In part 1, I pre-processed matrix of Berstein polynomial coefficients for use in `BezierPatch::evaluate(...)`. My reasoning for this was that in evaluating the Bezier patch, a pre-processed matrix will multiply out more efficiently than recursively calling De Casteljau's algorithm. (And, it's slightly less complex to implement!) Then, inside `evaluate(...)`, I calculated the variable vectors `uVect` and `vVect` of the Berstein polynomial, and dotted those vectors with the coefficient matrix to get the actual Berstein polynomials, `ut` and `uv`. The u-directional polynomial then multiplies with the 4 sets of 4 u-oriented control points of the Bezier Patch to interpolate for a set of 4 v-oriented "secondary" control points. Finally, the v-directional polynomial is multiplied with the secondary control points, returning the 3D point at `(u,v)` on the Bezier patch.


In `add2mesh(...)`, I evaluate a Bezier Patch and tesselate the patch uniformly on to an 8x8 grid with 128 triangles. I iterate integer-wise (to avoid accumulating error) using 2 for-loops to cover an 8x8 grid. On all of the unit grids, I first evaluate the Bezier Patch for the top-left, bottom-left, and bottom-right points and pass those 3D points into `addTriangle(...)`. Then, as a slight optimization, I only need to further evaluate the Bezier Patch for the remaining top-right point; I pass those points into `addTriangle(...)`.



![part1-1.webp](/assets/webp/cs184/2/part1-1.webp){:.lead width="1072" height="774" loading="lazy"}
A rendering of teapot.bez: my `BezierPatch::evaluate(...)` and `add2mesh(...)` seems to work!
{:.figcaption}

## Part 2: Average normals for half-edge meshes
In Part 2, I computed the area-weighted unit normal for a given vertex. In computing an area normal, you need to cross-product two vectors, both of which are based on the given vertex and point towards the given vertex's neighbors. Because the half-edges are oriented counter-clockwise on a surface, the traversal of the nieghboring vertices happen in a clockwise fashion. (Due to the `h_twin = h->twin(); h = h_twin->next();` algorithm.) So to obtain positive products from the cross-product, one has to take the vector (given vertex to new neighbor) X vector (given vertex to old neighbor).
 Before looping, I first initialize an "old" vector by subtracting the first neighboring vertex's position with the given vertex's position. Then I traverse the neighboring vertices: computing the "new" vector by subtracting the new neighbor's position with the given vertex's position; crossing the "new" and "old" vectors, adding that product to a running sum vector; and updating the "old" vector to have the "new" vector's value. After terminating the traversal, the normalized vector of the running sum is returned. That normalized vector is the area-weighted unit normal for the given vertex.

The traversal algorithm can handle vertices on boundaries. Boundary halfedges do not mess up the traversal, `h->twin()->next()->vertex()` correctly gives a valid vertex even if the vertex is on a boundary. Vertices on boundaries are still valid and have 3D positions.



![part2-dae0.webp](/assets/webp/cs184/2/part2-dae0.webp){:.lead width="1072" height="774" loading="lazy"}
Dae teapot without smoothed normals. The surface of the teapot is blocky, as it is only being rendered using uninterpolated face normals.
{:.figcaption}


![part2-dae1.webp](/assets/webp/cs184/2/part2-dae1.webp){:.lead width="1072" height="774" loading="lazy"}
Wireframe dae teapot with smooth surface, as a result of interpolating the vertex normals.
{:.figcaption}


![part2-dae2.webp](/assets/webp/cs184/2/part2-dae2.webp){:.lead width="1072" height="774" loading="lazy"}
Dae teapot with smooth surface, as a result of interpolating the vertex normals.
{:.figcaption}


![part2-bez1.webp](/assets/webp/cs184/2/part2-bez1.webp){:.lead width="1072" height="774" loading="lazy"}
Wireframe bez teapot with smooth surface, as a result of interpolating the vertex normals.
{:.figcaption}


![part2-bez2.webp](/assets/webp/cs184/2/part2-bez2.webp){:.lead width="1072" height="774" loading="lazy"}
Bez teapot with smooth surface, as a result of interpolating the vertex normals.
{:.figcaption}

## Part 3: Edge Flip
In Part 3, I implemented `HalfedgeMesh::flipEdge(...)`, which flips the edge that is passed in and returns an iterator to that flipped edge. First, I check if the edge is on a boundary: if it is, then the passed-in edge is returned immediately. Otherwise, it is safe to flip the edge!

I drew a diagram of the edge flip in the code, to clarify the function for myself and for potential readers.


![part3-diagram.webp](/assets/webp/cs184/2/part3-diagram.webp){:.lead width="478" height="596" loading="lazy"}
I modeled the edge-to-be-flipped as an Top-to-Botttom edge bisecting a quad into two triangles. This assignment of directions is arbitrary, but clarifies the operations necessary to flip the edge.
{:.figcaption}
Because we are only flipping an existing edge, and not creating any new elements in the mesh, we do not need to call any of the new*() functions. To prepare for the edge flip, I get all of the necessary data structures. Then I reassign the structures accordingly: the given edge still points to the same halfedge, but the corresponding two halfedges have their vertex()'s and next()'s changed. Likewise, the face()'s of some strucutres are changed. To ensure correctness, the halfedge()'s of the top and bottom vertices are reassigned, in case they used to point at the given edge's halfedges. The next()'s of all of the involved halfedges are reassigned. Note that the left and right vertices' halfedge()'s do not have to be reassigned. A vertex only has to store one of it's halfedge()'s; the old halfedge()'s are still valid assignments.


![part3-1.webp](/assets/webp/cs184/2/part3-1.webp){:.lead width="1072" height="774" loading="lazy"}
A bunch of edges on the face of the teapot are flipped to make a face!
{:.figcaption}


![part3-2.webp](/assets/webp/cs184/2/part3-2.webp){:.lead width="1072" height="774" loading="lazy"}
Edges on the lid of the teapot are flipped to make an interesting pattern. Additionally, the boundary case was tested and no boundary edges were flippable.
{:.figcaption}

## Part 4: Edge Split
In Part 4, I split edges! Two triangles with on shared edge is split into four separate triangles. This part similar to part 3, except now we have to allocate new elements into the mesh. Again, I drew diagrams in the code to help visualize the need assignments of the structures.



![part4-diagram1.webp](/assets/webp/cs184/2/part4-diagram1.webp){:.lead width="535" height="539" loading="lazy"}
The half edges are labeled according to their base vertex and the vertex to which they point. So for example `m2t` points from the midpoint to the top vertex, and `l2b` points from the left vertex to the bottom vertex. I do make some redundant variable assignments: this is for clarification purposes. Again, the directional labels are arbitrary, but help to clarify the necessary operations to split an edge.
{:.figcaption}


![part4-diagram2.webp](/assets/webp/cs184/2/part4-diagram2.webp){:.lead width="505" height="457" loading="lazy"}
Extra Credit Boundary Case: Similar to the above digram, but this is for the boundary case! There are less assignments needed, but dealing with the boundary edges are a bit trickier.
{:.figcaption}

Basically, what I did for this part was the same as in Edge Flip. I first get all the relevant data structures. But then this time, I also allocate new structures by calling `new*()`. Then, I calculate the midpoint's position - it is the only new structure that requires a position assignment. Then, I go through the vertices, faces, and edges to set their appropriate halfedge()'s. Finally, I use the halfedge's `setNeighbors(...)` function compactly set each halfedge's pointers (iterators). While some of the assignment in `setNeighbors(...)` is redundant, it ensures correctness by requiring everything to be updated.

I also handle the case where the edge to be split is on a boundary. In that case, less new structures are allocated into the mesh, but assignments are slightly trickier. Only the non-boundary face is split into two and receives a new edge; the boundary face is left alone. At the start, before getting the relevant data structures, we obtain `h` such that `h` is not on the boundary. This orients the triangle so that the face to be split is on the left side of the given edge, and the boundary face is on the right side of the given edge. Then, we obtain the relevant structures and the correct assignments.


![part4-1.webp](/assets/webp/cs184/2/part4-1.webp){:.lead width="1072" height="774" loading="lazy"}
Edge split demonstrated across multiple faces and edges on the wavy cube.
{:.figcaption}


![part4-2.webp](/assets/webp/cs184/2/part4-2.webp){:.lead width="1072" height="774" loading="lazy"}
Extra Credit Boundary Case: The boundary edges of the bez teapot are successfully split repeatedly. There are many repeated splits shown on the right side of the lid.
{:.figcaption}

## Part 5: Upsampling via Loop Subdivision
Conceptually, upsampling was initially a bit difficult for me to understand and get started with. However, after looking through the documentation in student `code.cpp` and `halfEdgeMesh.h`, things became much clearer. The actual implementation of this part was decently quick, except for a bug I ran into.

In implementing upsampling via loop subdivision, I mostly just followed the TODO comments. First, I compute the new positions for all of the vertices in the input mesh, and mark their isNew as false. As for computing the positions based on the loop subdivision, I precomputed the fractions in the formula and assigned exact decimal values rather than assigning fraction to the variables. This should slightly increase numerical accuracy, and fairly improve performance by reducing the number of computations per vertex.

Then, I computed the updated vertex positions of the edges and stored the values in the edges' newPosition. The values stored will become the positions of the newly allocated vertices from subdivision/edge-splitting. Again, I have the formula's fractional formulas pre-computed. Here, I mark the edges' isNew as false. In this section and the next, I drew small diagrams in the code comments for clarification, as in the previous parts.


![part5-diagram.webp](/assets/webp/cs184/2/part5-diagram.webp){:.lead width="510" height="270" loading="lazy"}
A diagram to help with loop subdivision. The directions mentioned are arbitrary, but help to clarify the code.
{:.figcaption}

Then, I split every old edge of the mesh. The old edge becomes the "top" edge of the split quad. The "left" and "right" edges are marked isNew as true, since they are new. However, even though it is newly allocated, the "bottom" edge is marked isNew as false, since it was originally part of the old edge. For the purposes of edge-flipping, it is an old edge. The newly created midpoint-vertex for each subdivision, returned by `splitEdge(...)`, is marked as isNew true.

In this edge splitting section, I had to figure out how to iterate and split all of the edges of the original mesh and none of the new edges created by the splitting. This task was tougher than I realized, as it turned out that `mesh.edgesEnd()` returned an iterator and not a real reference value. This had the strange effect that the `end` variable increased whenever an edge was split, even if I only assign the `end` once as `mesh.edgesEnd()` once before the loop. Thus the end was every-increasing, leading to an infinite loop. So, while iterating through the original edges in the previous section (Computing the `newPosition` values), I add the edge to a list of `EdgeIters`s, called `edgesToSplit`. Then in this splitting section, I iterate through the list of `EdgeIters`, rather than through using the iterator provided by `mesh.edgesBegin()` and `mesh.edgesEnd()`. The list is finite and does not grow with each splitting.

In the next section, I flip any edge that connects to an old vertex and a new vertex. To do this, I iterate through all edges of the newly subdivided mesh. If the edge was part of an old edge, with `isNew` marked as false, immediately skip to the next iteration. Otherwise, the edge is new. So, we check whether it's vertices are of opposite `isNew`-ness, and flip the edge if they are: the two vertices are accessed via the edge's halfedge and the corresponding twin. If one if the vertices has `(isNew == true)` and the other has `(isNew == false)`, we flip that edge. This operation ensures that the triangles are loop subdivided into equilateral-like shapes, rather than being overly skinny. (As explained by Denis Zorin's diagram in the assignment spec.)

Finally all of the vertices' positions are updated. If the vertex is an vertex from the old mesh, with isNew false, it's position is updated with the value stored in the newPosition variable. Otherwise, if the vertex isNew, we assign it the value that was stored in it's old edge's newPosition variable. By construction of the previous parts' functions, the new vertex should naturally point to a halfedge whose edge contains the correct corresponding newPosition value. However, a small bug arose in my code. My new vertices did not all point to the correct halfedge whose edge had the newPosition value. I suspect that is becase my edge-flipping function "unstably" reassigns the vertices' halfedges, without respect to whether they are new or not. (This bug created interesting patterns in my meshes though.) To account for the "unstable" edge flip, I have an extra condition/loop inside this section. I have a HalfedgeIter traverse the incident edges to the vertex. The traversal stops when a non-New non-Zero-newPosition edge is found -- which is guaranteed to exist because it is exactly the edge that the new vertex was supposed to point to, before the edge flipping. This resolved the bug and thus subdivision happens correctly


![part5-ok1.webp](/assets/webp/cs184/2/part5-ok1.webp){:.lead width="1072" height="774" loading="lazy"}
A subdivided teapot.
{:.figcaption}


![part5-ok2.webp](/assets/webp/cs184/2/part5-ok2.webp){:.lead width="1072" height="774" loading="lazy"}
A teapot even more subdivided, zoomed in. Oh, my fps!
{:.figcaption}


![part5-ok22.webp](/assets/webp/cs184/2/part5-ok22.webp){:.lead width="1072" height="774" loading="lazy"}
A teapot, super subdivided. R.I.P. fps.
{:.figcaption}


![part5-ok3.webp](/assets/webp/cs184/2/part5-ok3.webp){:.lead width="1072" height="774" loading="lazy"}
A torus, subdivided many, many times. Interestingly, the edges form a moire pattern!
{:.figcaption}


![part5-error1.webp](/assets/webp/cs184/2/part5-error1.webp){:.lead width="1072" height="774" loading="lazy"}
A picture of the bug I experienced.
{:.figcaption}


![part5-error2.webp](/assets/webp/cs184/2/part5-error2.webp){:.lead width="1072" height="774" loading="lazy"}
Another picture of the bug I experienced.
{:.figcaption}


![part5-error3.webp](/assets/webp/cs184/2/part5-error3.webp){:.lead width="1072" height="774" loading="lazy"}
Yet another picture of the bug I experienced, like a spikey snow flake!
{:.figcaption}


![part5-error4.webp](/assets/webp/cs184/2/part5-error4.webp){:.lead width="1072" height="774" loading="lazy"}
A vaguely creepy, buggy rendering of Max Planck, :(.
{:.figcaption}

Here, I tried subdividing cube.dae. After several subdivision, the cube takes on an asymmetric shape.


![part5-cube1.webp](/assets/webp/cs184/2/part5-cube1.webp){:.lead width="1072" height="774" loading="lazy"}
An asymmetrically subdivided cube.
{:.figcaption}
This arises because of the original mesh topology on the cube. The subdivision algorithm smoothes out vertices higher degree more than vertices with lower degree. So, in the low-poly cube, the higher degree vertices get smushed together moreso than the low degree vertices, leading to asymmetry. This asymmetry in subdivision can be solved by equalizing the vertices' degrees as much as possible before subdivision. Simply flipping edges before subdividing will not solve the problem: the original topology only has a limited amount of edges to go around, some vertices will always have a different degree than other vertices. The subdivision would produce an asymmetric mesh in that case. However, one can also split the cube's face edges before subdivision. The splitting of the edges can be used to equalize the degrees of all the original vertices. This allows the mesh to subdivide symmetrically. When the cube's respective symmetric vertices have the same degree, the cube subdivides symmetrically.


![part5-cube2-1.webp](/assets/webp/cs184/2/part5-cube2-1.webp){:.lead width="1072" height="774" loading="lazy"}
In this case, I only flipped edges before subdividing. The result is still an asymmetric mesh.
{:.figcaption}


![part5-cube2-2.webp](/assets/webp/cs184/2/part5-cube2-2.webp){:.lead width="1072" height="774" loading="lazy"}
In this case, I only flipped edges before subdividing. The result is still an asymmetric mesh.
{:.figcaption}


![part5-cube2-3.webp](/assets/webp/cs184/2/part5-cube2-3.webp){:.lead width="1072" height="774" loading="lazy"}
In this case, I only flipped edges before subdividing. The result is still an asymmetric mesh.
{:.figcaption}


![part5-cube2-4.webp](/assets/webp/cs184/2/part5-cube2-4.webp){:.lead width="1072" height="774" loading="lazy"}
In this case, I only flipped edges before subdividing. The result is still an asymmetric mesh.
{:.figcaption}


![part5-cube3-1.webp](/assets/webp/cs184/2/part5-cube3-1.webp){:.lead width="1072" height="774" loading="lazy"}
In this case, I split the cube's face edges before subdividing. The result is a symmetric mesh.
{:.figcaption}


![part5-cube3-2.webp](/assets/webp/cs184/2/part5-cube3-2.webp){:.lead width="1072" height="774" loading="lazy"}
In this case, I split the cube's face edges before subdividing. The result is a symmetric mesh.
{:.figcaption}


![part5-cube3-3.webp](/assets/webp/cs184/2/part5-cube3-3.webp){:.lead width="1072" height="774" loading="lazy"}
In this case, I split the cube's face edges before subdividing. The result is a symmetric mesh.
{:.figcaption}

## Part 6: Fun with Shaders
In part 6, I had fun with shaders! One of the more annoying parts of this section is type-matching: that everything is in floats and nothing takes ints.

I simply referred to the given formulas and the lecture slides to implement this part. For Phong shading, I calculated the light direction vector, reflection direction vector, and the eye direction vector. Then, I used those values to calculate the specular highlights: specular is first the max of (reflection direction dot eye direction, and 0.0), then the specular is raised a certain power and multiplied by a certain coefficient. (I defined spec_power as 8.0 and spec_coeff as 1.0 at the top of the frag file.) The diffuse light is the absolute value of eye direction dot the normalized normal. The ambient light was chosen as a very dark gray. Phong shading adds together the specular, diffuse, and ambient lights.


![part6-phong.webp](/assets/webp/cs184/2/part6-phong.webp){:.lead width="1072" height="774" loading="lazy"}
Red Phong shading of the teapot, with mesh subdivision to make the surface smoother. The specular highlight was raised to a the power of 8.0 to make the specular lighting spot tight and concentrated. Lower powers make the specular highlight cover a majority of the teapot at this angle.
{:.figcaption}


![part6-phong2.webp](/assets/webp/cs184/2/part6-phong2.webp){:.lead width="1072" height="774" loading="lazy"}
Red Phong shading of the teapot, with mesh subdivision to make the surface smoother. This specular highlight was raised to a high power (24.0) to make the specular lighting spot even tighter and more concentrated.
{:.figcaption}


![part6-phong3.webp](/assets/webp/cs184/2/part6-phong3.webp){:.lead width="1072" height="774" loading="lazy"}
Red Phong shading of the cow, with largely noticeable specular highlights.
{:.figcaption}


![part6-phong4.webp](/assets/webp/cs184/2/part6-phong4.webp){:.lead width="1072" height="774" loading="lazy"}
A Phong shading of a subdivided torus.
{:.figcaption}


![part6-phrong1.webp](/assets/webp/cs184/2/part6-phrong1.webp){:.lead width="1072" height="774" loading="lazy"}
Rainbow Teapot!: A cool, but incorrect implementation of phong shading! I turned this into an extra selectable shader later on for extra credit.
{:.figcaption}


![part6-phrong2.webp](/assets/webp/cs184/2/part6-phrong2.webp){:.lead width="1072" height="774" loading="lazy"}
Rainbow Cow!: A cool, but incorrect implementation of phong shading! I turned this into an extra selectable shader later on for extra credit.
{:.figcaption}
This section was fairly straight forward. For environment mapping, I calculated the reflection direction as given by the formula in the specs. Then, I calculated theta and phi as according to their 3D geometric meanings, using the atan(2) and acos() functions. Then, I converted theta and phi to uv-coordinates using the other forumula in the spces. The uv-coordinates were then passed into texture2D of the envmap, in order to obtain the corresponding texel color. The color is returned for texture2D as an RGBA value, so I had to convert it to the corresponding RGB value, before finally returning the vec3 color.


![part6-envmap1.webp](/assets/webp/cs184/2/part6-envmap1.webp){:.lead width="1072" height="774" loading="lazy"}
Teapot with the given environment map. This teapot looks like it's made of metal!
{:.figcaption}

I did quite a bit of extra credit work for this part and the next part combined, so I'll put that into a new part by itself! It's below Part 7 below.


## Part 7: Design your own mesh!
This part required that I use a mouse, but even then the controls were difficult to learn. In other applications, I had been used using LMB to select and drag select items, using RMB to deselect items or to rotate the view. So, it took me a long while to adjust to using RMB to select and drag, LMB to confirm, and not having a mouse button to automatically/fully deselect items. (I had to use weird combinations of command, control, and shift.) That being said, this part was incredibly fun. Though, great patience is required to be able to adjust all of the vertices, edges, and faces, to exactly how one pictures it. I have a newfound respect for professional 3D modelers. Even though I had an image in my mind, it was difficult to produce a model that remotely resembled my original idea.

I made a 3D Pikachu! (Sort of. It's not particularly spectacular.)


![part7-1.webp](/assets/webp/cs184/2/part7-1.webp){:.lead width="1072" height="774" loading="lazy"}
My Pikachu collada/dae mesh! It took several multiple-hour iterations to get a good balance of polygons.
{:.figcaption}


![part7-11.webp](/assets/webp/cs184/2/part7-11.webp){:.lead width="1072" height="774" loading="lazy"}
A red-phong shaded Pikachu.
{:.figcaption}


![part7-2.webp](/assets/webp/cs184/2/part7-2.webp){:.lead width="1072" height="774" loading="lazy"}
Subdividing my Pikachu mesh is a bad idea. The sharp ears become horn-like spikes. The tail becomes a crumpled mess. And, just meh.
{:.figcaption}


![part7-3.webp](/assets/webp/cs184/2/part7-3.webp){:.lead width="1072" height="774" loading="lazy"}
Further subdivision makes the Pikachu mesh look even worse. This is likely because a Pikachu is not a natural object and has jagged edges.
{:.figcaption}


![part7-4.webp](/assets/webp/cs184/2/part7-4.webp){:.lead width="1072" height="774" loading="lazy"}
Further subdivision makes the Pikachu mesh look even worse. This is likely because a Pikachu is not a natural object and has jagged edges.
{:.figcaption}

## Part 8: Extra Stuff from Part 6 and 7!
I uv-unwrapped my Pikachu mesh in Blender, and 3D texture-painted it.


![part8-1.webp](/assets/webp/cs184/2/part8-1.webp){:.lead width="1552" height="919" loading="lazy"}
My Pikachu in Blender! It turns out that Pikachu has a pretty creepy face before the whites of his eyes are drawn in: they're just black blobs, and the face looks like something from JigSaw. No picture is provided of that for your convenience.
{:.figcaption}


![part8-2-1.webp](/assets/webp/cs184/2/part8-2-1.webp){:.lead width="1072" height="774" loading="lazy"}
I tried applying that complex UV-texture map in meshEdit, but it didn't work. I've tried multiple implementations, but couldn't get the textures to map to the triangles correctly. I suspect that the frag file does not have enough positional information to correctly map the texture by triangles?
{:.figcaption}


![part8-2-2.webp](/assets/webp/cs184/2/part8-2-2.webp){:.lead width="1072" height="774" loading="lazy"}
I tried applying that complex UV-texture map in meshEdit, but it didn't work. I've tried multiple implementations, but couldn't get the textures to map to the triangles correctly. I suspect that the frag file does not have enough positional information to correctly map the texture by triangles?
{:.figcaption}


![part8-2-3.webp](/assets/webp/cs184/2/part8-2-3.webp){:.lead width="1072" height="774" loading="lazy"}
Still not working... :(
{:.figcaption}


![part8-2-4.webp](/assets/webp/cs184/2/part8-2-4.webp){:.lead width="1072" height="774" loading="lazy"}
Nope :(
{:.figcaption}

However, I made some extra shaders!

For this part, I had to do some digging through the other code files. I had to make the renderer/shader program load extra textures! In `main.cpp`, new textures were loaded with `(GLuint tex = makeTex(FILE_PATH);)`, followed by a `glActiveTexture` and `glBindTexture`. In `meshEdit.cpp`, the corresponding textures were labeled through `(glUniform1i(glGetUniformLocation(shaderProgID, NAME), VAL);)`. This allowed me to have `uniform sampler2D NAME` at the top of my `frag` file, which in turned allowed for the sampling of texels through `vec4 texture2D(sample2D tex, vec2 uv)`.


With the power of fancy new textures, I made some extra shaders in frag:

I made a Pikachu face texture for the teapot. (This is a different texture than the UV-mapping from Blender).


![part8-3-1.webp](/assets/webp/cs184/2/part8-3-1.webp){:.lead width="1072" height="774" loading="lazy"}
Bright yellow Pikachu teapot, with specular highlights!
{:.figcaption}


![part8-3-2.webp](/assets/webp/cs184/2/part8-3-2.webp){:.lead width="1072" height="774" loading="lazy"}
Dull yellow Pikachu teapot, with specular highlights and diffuse shading!
{:.figcaption}
I also made my own version of Weilun's YinYang shader/texture!


![part8-4-1.webp](/assets/webp/cs184/2/part8-4-1.webp){:.lead width="1072" height="774" loading="lazy"}
A teapot with the YinYang symbol on it! This also uses specular and diffuse lighting.
{:.figcaption}
Then, I combined the Pikachu Face texture, the envMap, and the YinYang symbol onto one single teapot. Also, diffuse and specular lighting was included. To do this, I used a lot of if-statements/conditionals.


![part8-5-1.webp](/assets/webp/cs184/2/part8-5-1.webp){:.lead width="1072" height="774" loading="lazy"}
A "metal" teapot with a YinYang symbol on one side, and a Pikachu face on the other. Notice that the YinYang symbol appears to be "matte." The color contribution to the teapot of the envMap reflection inside the YinYang symbol is reduced. This decrease of reflected like makes that part of the teapot appear to be made of a different, somewhat matte material.
{:.figcaption}


![part8-5-2.webp](/assets/webp/cs184/2/part8-5-2.webp){:.lead width="1072" height="774" loading="lazy"}
The other side of the "metal" teapot has a Pikachu face. The pikachu face, still having contribution from the envMap reflection, looks as if it is a shiny vinyl/plastic sticker on the side of the teapot.
{:.figcaption}


![part8-5-3.webp](/assets/webp/cs184/2/part8-5-3.webp){:.lead width="1072" height="774" loading="lazy"}
The view from above.
{:.figcaption}

I was trying the above combo-texture/shader on different objects, when I saw an opportunity: The cow! This is yet another option available within frag.


![part8-6-1.webp](/assets/webp/cs184/2/part8-6-1.webp){:.lead width="1072" height="774" loading="lazy"}
The cow, shiny with the envMap, with a Pikachu face!
{:.figcaption}


![part8-6-2.webp](/assets/webp/cs184/2/part8-6-2.webp){:.lead width="1072" height="774" loading="lazy"}
But wait, the back of the cow's head also has a Pikachu face. There shall be no Voldemorts on my Pikachu cow!
{:.figcaption}


![part8-6-3.webp](/assets/webp/cs184/2/part8-6-3.webp){:.lead width="1072" height="774" loading="lazy"}
Using more if-statements, I adjusted the Pikachu face to only show up on the front of the cow's face.
{:.figcaption}


![part8-6-4.webp](/assets/webp/cs184/2/part8-6-4.webp){:.lead width="1072" height="774" loading="lazy"}
The shiny Pikachu-cow is fixed!
{:.figcaption}

Furthermore, I reimplemented the earlier rainbow-bug of the Phong shading. But this time, it's a feature!


![part6-phrong2.webp](/assets/webp/cs184/2/part6-phrong2.webp){:.lead width="1072" height="774" loading="lazy"}
The rainbow cow from before.
{:.figcaption}


![part8-7-1.webp](/assets/webp/cs184/2/part8-7-1.webp){:.lead width="1072" height="774" loading="lazy"}
A rainbow-shaded Pikachu model. It looks as if the Pikachu is crystalized.
{:.figcaption}

Finally, I combined my rainbow shader with the Pikachu-cow to get:


![part-final1.webp](/assets/webp/cs184/2/part-final1.webp){:.lead width="1440" height="900" loading="lazy"}
This beautiful abomination.
{:.figcaption}


![part-final2.webp](/assets/webp/cs184/2/part-final2.webp){:.lead width="700" height="860" loading="lazy"}
So majestic.
{:.figcaption}