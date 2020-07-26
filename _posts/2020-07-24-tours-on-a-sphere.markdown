---
layout: post
title:  "Tours on a sphere"
date:   2020-07-24 11:19:35 -0300
tags: mathematics geometry mathematica
---
A while ago I stumbled upon this nice geometry problem:

> Pick a point $$p$$ on the unit sphere and fix some length $$L$$. If we start at $$p$$ and move $$L$$ units of length northward, then $$L$$ eastward, $$L$$ southward and finally $$L$$ westward, do we end up at the same point we started from?

The answer, somewhat surprisingly, depends on the point $$p$$ we start from, although generically we do _not_ end up at the same place.This happens because when moving north or southward our trajectory lies on meridians, which are all [great circles][great-circles], while on the other hand the length of parallels depends on their latitude: as we go near the poles, parallels are shorter. For example, if we start up on a point close to the equator but lying on the northern hemisphere, our first trip on the northernmost parallel sweeps up a broader angle than the southernmost, since the first one is shorter. In this way we end up on a point slightly eastward than from where we started, as the following picture shows:

{:refdef: style="text-align: center;"}
![A failed tour round a sphere](/assets/2020-07-24-sphere1.gif)
{: refdef}

The dual problem shows up if most of our trajectory lies on the southern hemisphere:

{:refdef: style="text-align: center;"}
![Another failed tour round a sphere](/assets/2020-07-24-sphere2.gif)
{: refdef}

An easy way to come up with points which do produce closed trajectories is just picking any point lying exactly $$L/2$$ units of length to the south of the equator: in this way our first northward trip lands on a parallel lying exactly $$L/2$$ units of length to the _north_ of the equator, and since both parallels now have the same length we don't have the same problem as before:

{:refdef: style="text-align: center;"}
![An easy succesful tour round a sphere](/assets/2020-07-24-sphere3.gif)
{: refdef}

We will now try to describe the coordinates of the end point more precisely. Using mathematicians' convention for [spherical coordinates][spherical-coords], if our starting point is $$p = (\cos(\theta)\sin(\phi), \sin(\theta )\sin(\phi), \cos(\phi))$$ then our first stop is $$(\cos(\theta)\sin(\phi-L), \sin(\theta)\sin(\phi-L), \cos(\phi-L))$$. The coordinates of our second stop depend on the length of the parallel we are now standing on, which is exactly $$2 \pi \sin(\phi-L)$$. The angle swept by an arc of length $$L$$ in such a circle is then $$L/\sin(\phi-L)$$, and so our second stop is
{:refdef: style="text-align: center;"}
$$(\cos(\theta+L/\sin(\phi-L))\sin(\phi-L), \sin(\theta+L/\sin(\phi-L))\sin(\phi-L), \cos(\phi-L))$$.
{: refdef}
Our third stop is then 
{:refdef: style="text-align: center;"}
$$(\cos(\theta+L/\sin(\phi-L))\sin(\phi), \sin(\theta+L/\sin(\phi-L))\sin(\phi), \cos(\phi))$$
{: refdef}
and finally our last stop is 
{:refdef: style="text-align: center;"}
$$(\cos(\theta+L/\sin(\phi-L)-L/\sin(\phi))\sin(\phi), \sin(\theta+L/\sin(\phi-L)-L/\sin(\phi))\sin(\phi), \cos(\phi))$$,
{: refdef}
so we will end up with a closed trajectory iff $$L/\sin(\phi-L)-L/\sin(\phi)$$ is an integral multiple of $$2\pi$$. From this we also see that the starting azimuthal angle $$\theta$$ is not relevant at all and that we always end up on the same parallel we started from.

The following is the Mathematica snippet I used to generate the graphics above:

{% highlight r %}
sphereplot = Show[Graphics3D[{Opacity[0.5], Sphere[]}],
  ParametricPlot3D[{Cos[τ], Sin[τ], 0}, {τ, 0, 2*Pi}, PlotStyle -> {Gray, Dashed}],
  ParametricPlot3D[{Cos[θ]*Sin[ϕ - τ], Sin[θ]*Sin[ϕ - τ], Cos[ϕ - τ]}, {τ, 0, L}],
  ParametricPlot3D[{Cos[θ + τ]*Sin[ϕ - L], Sin[θ + τ]*Sin[ϕ - L], Cos[ϕ - L]}, {τ, 0, L/Sin[ϕ - L]}],
  ParametricPlot3D[{Cos[θ + L/Sin[ϕ - L]]*Sin[ϕ - L + τ], Sin[θ + L/Sin[ϕ - L]]*Sin[ϕ - L + τ], Cos[ϕ - L + τ]}, {τ, 0, L}],
  ParametricPlot3D[{Cos[θ + L/Sin[ϕ - L] - τ]*Sin[ϕ], Sin[θ + L/Sin[ϕ - L] - τ]*Sin[ϕ], Cos[ϕ]}, {τ, 0, L/Sin[ϕ]}],
  Boxed -> False]; 

point[τ_ /; τ <= 1] = {Cos[θ]*Sin[ϕ - L*τ], Sin[θ]*Sin[ϕ - L*τ], Cos[ϕ - L*τ]};
point[τ_ /; 1 <= τ && τ <= 2] = With[{x = τ - 1}, {Cos[θ + L*(x/Sin[ϕ - L])]*Sin[ϕ - L], Sin[θ + L*(x/Sin[ϕ - L])]*Sin[ϕ - L], Cos[ϕ - L]}];
point[τ_ /; 2 <= τ && τ <= 3] = With[{x = L*(τ - 2)}, {Cos[θ + L/Sin[ϕ - L]]*Sin[ϕ - L + x], Sin[θ + L/Sin[ϕ - L]]*Sin[ϕ - L + x], Cos[ϕ - L + x]}]; 
point[τ_ /; 3 <= τ && τ <= 4] = With[{x = L*((τ - 3)/Sin[ϕ])}, {Cos[θ + L/Sin[ϕ - L] - x]*Sin[ϕ], Sin[θ + L/Sin[ϕ - L] - x]*Sin[ϕ], Cos[ϕ]}]; 

ball[τ_] = Show[sphereplot, Graphics3D[{Red, Sphere[point[τ], 0.03]}], ViewPoint -> {1.8, -2.8, 1.5}, ViewVertical -> {0.255, -0.4, 0.9}]; 

frames = ball /@ Range[0, 4, .05];
{% endhighlight %}

You can then either animate ``frames`` in Mathematica using ``ListAnimate@frames`` or export it using ``Export["sphere.gif", frames]``.

If you'd like to run the code above, you have to set values for ``L``,  ``θ`` and ``ϕ``. Some sensible defaults are ``L = 0.6`` and ``θ = π``. As for ``ϕ``, you can find values for it which produce closed trajectories by using Mathematica's numerical solver:

{% highlight r%}
Solve[L/Sin[α - L] - L/Sin[α] == 2*π, α]; 
{% endhighlight %}  

You can come up with some funky solutions fooling around with different values of ``L``:

{:refdef: style="text-align: center;"}
![Funky solution 1](/assets/2020-07-24-sphere4.gif)
![Funky solution 2](/assets/2020-07-24-sphere5.gif)
![Funky solution 3](/assets/2020-07-24-sphere6.gif)
![Funky solution 4](/assets/2020-07-24-sphere7.gif)
{: refdef}

[great-circles]: https://en.wikipedia.org/wiki/Great_circle
[spherical-coords]: https://en.wikipedia.org/wiki/Spherical_coordinate_system