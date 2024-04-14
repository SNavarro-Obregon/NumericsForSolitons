# Object oriented coding

## Why?

Suppose you have a kink and you’d like to calculate it’s energy. To caculate this, you need to have the kink field, the grid, maybe some parameters and knowledge of the boundary conditions. You could write a `calculate_energy` function. Because it needs all these things, you end up passing them all to the function. So to use it you need to write code that looks something like

```
calculate_energy(my_kink, grid, parameters, boundary_conditions)
```

This is ok, but can get out of hand quickly.

Another option is to represent your kink as an _object_ (as a class in c++, a struct in Julia). Think of this as a template of your field, which bundles all its vital information together. So you might make a class which represents a kink with this structure

``` title=“Pseudocode”
class Kink:
	field
	grid
	parameters
	boundary_conditions
```

You would then make your kink `my_kink = Kink(…)`. Here `my_kink` is an object of class (or struct) type Kink. When you pass it to the energy function, you only need to write…

```
calculate_energy(my_kink)
```

…since my_kink contains everything you need to know. There are more advantages than just making your code looks nicer. Classes and objects mean your field is closely tied together with the grid it’s on etc. So it should be harder to make mistakes.

You can also define methods. These are functions which are special for your classes. Then you can do things like: `energy = my_kink.calculate_energy()`. Nice!

## Examples

Let’s make the aforementioned kink. To be more specific, we’ll make a class with:
1. an array representing the field
2. a float representing the lattice_spacing
3. an integer representing the number of lattice points,
4.  a list of floats representing parameters
5. a string representing the bounadry conditions
This isn’t neccisarly a perfect structure; but it’s good to try out making a class!

Here we go:

````{tab-set-code}

```{code-block} Julia
mutable struct Kink
    field::Array{Float64}
    ls::Float64
    lp::Int64
    pars::Vector{Float64}
    boundary_conditions::String
end

```

```{code-block} other

```

````

Now how do we make a `Kink`? First, you need to make an constructor. This varies a bit by language. One important point is that you make some values needed for constructor and while some can be optional; and go to a default value. In this example we’ll require the user to give the lattice spacing and lattice points but not the field, which will default to zeros. We’ll also give pars the default value of an array with one zero and the boundary_conditions equal to “Dirichlet”:

````{tab-set-code}

```{code-block} Julia
Kink(lp::Int64, ls::Float64; pars = [0], boundary_conditions=“Dirichlet" ) = Kink(zeros(lp), ls, lp, pars, boundary_conditions)
```

```{code-block} other
???
```

````

Then you can make `my_kink`, on a grid of 100 points with lattice spacing 0.2 as follows: (The other elements of the class will take their default values)

````{tab-set-code}

```{code-block} Julia
my_kink = Kink(100, 0.2)
```

```{code-block} other
???
```

````

Now we can do things like print the field values, or change the parameters as follows:

````{tab-set-code}

```{code-block} Julia
print(my_kink.field)
my_kink.pars[0] = [2.0]

```

```{code-block} other
???
```

````

It might be a good idea to make a Grid class and a Parameters class. Then your Kink class will contain the field, a Grid and a Parameters.

## Resources

[Constructors in Julia](https://docs.julialang.org/en/v1/manual/constructors/)



