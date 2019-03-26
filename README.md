# Polymer

A Julia implementation of the pivot algorithm: a Markov chain Monte carlo (MCMC) sampler for the self-avoiding walk (SAW) model of a linear polymer chain.

<table style="width:100%">
	<!-- Images -->
	<tr>
		<td><img src="examples/plot_10000_100000.svg" style="width:100%" /></td>
		<td><img src="examples/anim_100_10000_pre_100000.gif" style="width:100%" /></td>
	</tr>
	<!-- Captions -->
	<tr>
		<td><font size="1">2D SAW with 10000 steps</font></td>
		<td><font size="1">Pivot algorithm in 2D</font></td>
	</tr>
	<!-- Images -->
	<tr>
		<td><img src="examples/plot3d_10000_100000.png" style="width:100%" /></td>
		<td><img src="examples/anim3d_100_10000_pre_100000.gif" style="width:100%" /></td>
	</tr>
	<!-- Captions -->
	<tr>
		<td><font size="1">3D SAW with 10000 steps</font></td>
		<td><font size="1">Pivot algorithm in 3D</font></td>
	</tr>
</table>

### Related

My other repository, [*saw*](https://github.com/bencwallace/saw) includes a Python implementation of the pivot algorithm (as well as of the Metropolis-Hastings algorithm for simulating other models of linear polymers), emphasizes object-oriented design, and includes a pedagogical explanation of self-avoiding walk and MCMC methods in a Jupyter [notebook](https://github.com/bencwallace/saw/blob/master/saw-simulation.ipynb).

*Polymer*, on the other hand, places emphasis on speed of computation for the pivot algorithm using [Julia](https://julialang.org/).

## Optimization

This implementation of the pivot algorithm is optimized in the following ways.

* Lattice rotations are represented as `SparseArray` objects. Matrix multiplication by a sparse array can be performed in linear (rather than quadratic) time (in the dimension). The pivot algorithm performs linearly many (in the number of polymer steps) matrix multiplications *per iteration*. An additional advantage of sparse arrays is that they require far less memory.
* Prior to pivoting, the initial (un-pivoted) segment of a walk is converted to a `Set` object, which is a type of hash table, allowing for constant time lookups (as opposed to linear or, at best, logarithmic time for searching an array or list). The conversion itself requires linear time but need only be performed once (per iteration), whereas a linear number of lookups is required. Thus, the speedup per iteration is from quadratic (or at least super-linear) to linear time.

The following optimization would also be desirable.

* It should be possible to parallelize the pivot operation since every point on the tail (pivoted) segment of a walk is pivoted independently. This should lead to a roughly linear (in the number of available CPU cores) speedup.

## Usage

### Setup

With Julia [downloaded](https://julialang.org/downloads/) and [installed](https://julialang.org/downloads/platform.html), download `pivot.jl`, `rot.jl`, and `utils.jl`, navigate to the directory where you downloaded these files, and run `julia`.

### Examples

Initialize a 3-dimensional polymer as a straight line with 1000 steps:

```julia
include("pivot.jl")

poly3d = line(1000, 3);
```

Initialize a 2-dimensional polymer and run the pivot algorithm for 10000 iterations:

```julia
poly = line(1000);
poly = mix(poly, 10000)
```

Plot a polymer:

```julia
include("utils.jl")

poly_plot(poly)
```

Produce an animation of the pivot algorithm run for 100 *successful* steps and save it to "pivot_anim.gif":

```julia
anim(100, line(100), "pivot_anim.gif")
```

## To do

Generate higher-quality (preferably vector) graphics. The following packages may be useful:

* [Gadfly.jl](http://gadflyjl.org/stable/)
* [Luxor.jl](http://juliagraphics.github.io/Luxor.jl/stable/)
