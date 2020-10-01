---
layout: blogpost
title: "Drawing doughnuts: adventures in the third dimension"
mathjax: yes
---


<video controls loop 
       style="float: right; padding: 0.5em; width: 10em; max-width: 50%;" 
       src="/assets/images/drawing-doughnuts.webm" 
       type="video/webm">
Animated game of life on a torus. Sorry, your browser doesn't support embedded videos.
</video>

The algorithm that produced the animation to the right was written in less than 100 lines of Java code, using a simple technique called *raymarching*. The aim of this blog post is to convince you that, with the right tricks, it's easy to draw 3D objects convincingly.

Raymarching
===========

The first problem to tackle is determining how to represent the torus.  Traditionally 3D graphics has been very polygon-focused, with objects being represented as a collection of triangular faces, which allows for maximal artist flexibility. However for our purposes, simple mathematical objects can be represented much more elegantly.

The key observation is that we are only interested in where the surface of the object is. We will represent our 3D object implicitly, as a *distance function* \\(f: \mathbb{R}^3 \mapsto \mathbb{R}\\) which takes a position vector in 3D space and tells us the minimum distance required to reach the surface from that position (considering all directions).[^analytic]

One can alternatively think of \\(f(\mathbf{x})\\) as the largest radius a sphere centered at \\(\mathbf{x}\\) could have before it touches the surface defined by \\(f\\).

Assuming we have this function already, we want to use it to solve the problem of determining which pixels the torus occupies on our screen. To do this we cast rays out of our camera, and see if they ever intersect the surface of our 3D object. If they do, we can later calculate the colour at that point.

Starting at a point \\(\mathbf{x}\_0\\) and moving along some unit direction vector \\(\hat{\mathbf{v}}\\), the distance function guarantees that it is safe to travel a distance of at least \\(f(\mathbf{x}\_0)\\) without fear of crossing the surface. Doing so we reach a new point \\(\mathbf{x}\_1 = \mathbf{x}\_0 + f(\mathbf{x}\_0) \hat{\mathbf{v}}\\). The *raymarching algorithm* simply does this in a loop: it checks whether a ray intersects our object by repeatedly moving a safe distance along the ray until it either converges on the object surface, or escapes to infinity.[^sdf]

<img src="/assets/images/drawing-doughnuts-march.svg"
     title="Raymarching example"
     alt="Raymarching example"
     style="width: 100%;"/>

In the 2D example above, we do raymarching twice, starting from the black points near the bottom edge and the top-left corner. The gray circle represents the first evaluation of the distance function \\(f(\mathbf{x}\_0)\\); it indicates the region in which it is safe to go. We extend our rays to the point \\(\mathbf{x}\_1\\) drawn in red, and evaluate the distance function again: \\(f(\mathbf{x}\_1)\\) is represented by the red circle. This process continues until either we cannot go any further, meaning that we have hit the surface, or we escape to infinity. The top ray is an example of the former, and the bottom the latter.

Pseudocode:

```glsl
EPSILON = .001;
INFINITY = 100.;

// startPos is the position vector of the start of our ray (= camera position).
// viewDir is the normalised vector in the direction of the ray.
// The return value is the point at which we hit the surface of the object, or
// null if the ray escapes.
vec3 raymarch(vec3 startPos, vec3 viewDir) {
    double totalDist = 0;
    while (true) {
        double dist = distanceFunction(startPos + totalDist*normalisedRayDir);
        totalDist += dist;
        if (dist < EPSILON) {
            // converged on surface
            return startPos + totalDist*normalisedRayDir;
        }
        if (totalDist > INFINITY) {
            // escaped to infinity
            return null;
        }
    }
}
```

The function argument `startPos` corresponds to \\(\mathbf{x}\_0\\), and `viewDir` corresponds to \\(\hat{\mathbf{v}}\\).[^viewdir]

It remains to define `distanceFunction` (corresponding to \\(f\\)) for a torus.

Constructing a distance function
================================

A torus can be constructed with two circles, as shown below. The green circle with radius \\(r\\) is revolved so that its center travels along the path of the red circle with radius \\(R\\).

<img src="/assets/images/drawing-doughnuts-torus.svg"
     title="Calculating the minimum distance to a torus"
     alt="Torus diagram"
     style="width: 100%;"/>

For simplicity we will set the torus such that the red circle lies in the \\(x\\)-\\(y\\) plane, with its center in the origin.

Given a point \\(\mathbf{x}\\), we want to determine the minimum distance \\(f(\mathbf{x})\\) to the surface of the torus.  The general idea is to determine the center \\(\mathbf{c}\\) of the green circle by projecting the point \\(\mathbf{x}\\) into the \\(x\\)-\\(y\\) plane, and then rescaling to the radius \\(R\\), as follows (taking \\(\hat{\mathbf{k}}\\) to be the unit vector along the \\(z\\)-axis):
<p markdown="0">
\begin{align}
\mathbf{x}_{xy} &= \mathbf{x} - \bigl(\mathbf{x}\cdot\hat{\mathbf{k}}\bigr)\mathbf{x} \\
\mathbf{c} &= \frac{R}{\left| \mathbf{x}_{xy} \right|} \mathbf{x}_{xy} \\
\end{align}
</p>
Then, the vector from the center \\(\mathbf{c}\\) to our point \\(\mathbf{x}\\) intersects the torus, giving the shortest distance as:
\\[f(\mathbf{x}) = \left| \mathbf{x} - \mathbf{c} \right| - r\\]

Note that this also gives us the normal \\(\hat{\mathbf{n}}\\) to the surface of the torus at the point of intersection, which we will need later:
\\[\hat{\mathbf{n}} = \frac{\mathbf{x} - \mathbf{c}}{\left| \mathbf{x} - \mathbf{c} \right|}\\]

Now, we need to determine what colour the torus should be at a particular pixel.


UV mapping
==========

UV mapping is the process of mapping a picture onto the surface of the torus. It is called this because we are finding a transformation from \\(xyz\\) space where the torus resides, into a set of new axes \\(u\\) and \\(v\\) which are the horizontal and vertical axes for the 2D texture that will be mapped onto the surface.

Imagine cutting through one circular cross section of the hollow torus. Think of this new shape as a hollow tube (a cylinder with no end-caps) bent around in a circle to make its two ends meet. Reverse this bending process to straighten out the cylinder. Now, cut down lengthways on one side of the tube to get a plane. The axes of this plane can serve as our \\(uv\\) axes.

Referring back to the 3D torus diagram above, this shows that the \\(u\\) axis corresponds to the angle that the red line makes with its "resting position" (say, the \\(x\\) axis), and the \\(v\\) axis corresponds to the angle that the green line makes with its "resting position" (say, the direction of the red line). Then, we have a simple way of calculating the \\(uv\\) coordinates from \\(x,y,z\\):

<p markdown="0">
\begin{align}
u &= \text{atan2}(\mathbf{c}_y, \mathbf{c}_x) \\
v &= \text{atan2}(\hat{\mathbf{n}}_z, \frac{\mathbf{c}}{R} \cdot \hat{\mathbf{n}}) \\
\end{align}
</p>

NB:

- I write \\(\mathbf{c}\_x\\) to mean the \\(x\\) component of vector \\(\mathbf{c}\\).
- The function \\(\text{atan2}(y,x)\\) calculates the CCW angle that \\((x, y)\\) makes with the positive \\(x\\)-axis. Beware that it takes its arguments in a funny order.

From this we can simply look up what the colour should be at the point \\((u, v)\\).[^rescaleuv] The final step is to calculate lighting.


Phong reflection
================

I was not overly concerned about accurate lighting, so I went for the simplest possible: a directional light source and Phong reflection.

The essence of the Phong reflection model is to approximate how a diffuse material reflects light, with as little computation as possible. It is based on the observation that the reflected light can be divided into three categories:

<div>
<img src="https://upload.wikimedia.org/wikipedia/commons/6/6b/Phong_components_version_4.png"
     style="width: 100%" />
<p style="margin-top: 0; text-align: right; font-size: 60%">Source:
<a href="https://commons.wikimedia.org/wiki/User:Rainwarrior">Brad Smith</a> @ 
<a href="https://en.wikipedia.org/wiki/File:Phong_components_version_4.png">Wikipedia</a>.
Available under a
<a href="https://creativecommons.org/licenses/by-sa/3.0/deed.en">
Creative Commons Attribution-ShareAlike license</a>.</p>
</div>

- the ambient term, which is a constant. It approximates light that has bounced off other surfaces and are indirectly illuminating our object. This term accounts for the fact that shadows are never completely black;
  \\[a = k\_a\\]
- the diffuse term, which is direct light that has scattered in a random direction upon hitting the object;
  \\[d = k\_d~\text{max} \\{ 0, \hat{\mathbf{l}} \cdot \hat{\mathbf{n}} \\} \\]
- the specular term, which is direct light that has reflected in a mirror-like way, creating a small bright spot of light. Note that this depends on the direction from which we view the object, \\(\hat{\mathbf{v}}\\);
  \\[s = k\_s \left(\text{max} \\left\\{ 0, \bigl( 2 (\hat{\mathbf{l}} \cdot \hat{\mathbf{n}}) \hat{\mathbf{n}} - \hat{\mathbf{l}} \bigr) \cdot \hat{\mathbf{v}} \\right\\} \right)^{k\_{shininess}} \\]

Then, once good-looking constants \\(k\_a\\), \\(k\_d\\), \\(k\_s\\), \\(k\_{shininess}\\) have been picked, the combined light intensity is simply \\[ i = a + d + s \\] (which can be clamped to between 0 and 1 as appropriate). The final RGB colour can be then obtained using an appropriate HSV to RGB conversion.

Wait, that's it?
================

Distance functions are very powerful; I have barely scratched the surface in this blog post. Arbitrarily complex scenes can be created by combining multiple distance functions in creative ways. For instance, the union of two different objects is simply the minimum of the two distance functions. In addition, it's possible to use the distance function to calculate shadows and ambient occlusion very cheaply.

If you found this post interesting, check out [ShaderToy](https://www.shadertoy.com/), an online WebGL shader sandbox and gallery, featuring all sorts of amazing effects fundamentally based upon [distance fields and raymarching](https://www.shadertoy.com/results?query=tag%3Draymarching).

[^analytic]:
    For a few shapes (e.g. spheres) it is easier to solve the ray intersection analytically without using raymarching; however torus intersections result in quartic equations which are tricky to solve. Raymarching can be applied in general situations, whilst still remaining fundamentally simple.

[^sdf]:
    For the purposes of this post, we ignore the case when we start inside the object. *Signed* distance functions are a simple extension that resolves any such ambiguity. If a signed distance function is negative at a point, then it indicates that the point is inside the object. The absolute value of the function remains the minimum distance to the surface.

[^rescaleuv]:
    It will be desirable to rescale \\((u, v)\\) first. This can be achieved easily, but be careful that the range of \\(\text{atan2}\\) may vary depending on the implementation. I ended up with something like
    \\[ (u', v') = (W \langle\frac{u+\pi}{2\pi}\rangle, H \langle\frac{v+\pi}{2\pi}\rangle) \\]
    where \\(\langle x \rangle\\) is the fractional part of \\(x\\), and \\(W\\), \\(H\\) are the dimensions of my texture.

[^viewdir]:
    The view direction is determined by which pixel \\((s\_x, s\_y)\\) on the screen is being processed:
    <p markdown="0">
    \begin{align}
    \mathbf{v} &= \left( \frac{2 s_x}{w} - 1, \frac{2 s_y}{h} - 1, v_z \right) \\
    \hat{\mathbf{v}} &= \frac{\mathbf{v}}{|\mathbf{v}|}
    \end{align}
    </p>
    where the \\(v\_z\\) component is the distance that the camera's sensor is in front of the focal point, and \\(w\\), \\(h\\) are the dimensions of the screen in pixels.

