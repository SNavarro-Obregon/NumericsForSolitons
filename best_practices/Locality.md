# Locality, locality locality 

There are three "localities" that are important.

## Locality of written code

Things that depend on each other should be near each other in your actual code.
If you have a function A which heavily uses B, put B beneath A. Then it's easy to find when you're developing.

## Locality of objects in memory

When doing a numerical calculation, you want everything you're using to be close to each other in memory.
The most extreme example of this is global parameters. Consider the following code

``` Julia
function a_big_sum(N)
    total = 0
    for i in 1:N
        total += lattice_spacing^N
    end
    return total
end

function a_big_sum_2(N, lattice_spacing)
    total = 0
    for i in 1:N
        total += lattice_spacing^N
    end
    return total
end
```

In `a_big_sum` we make a total. The parameter `lattice_spacing` is not defined in the function 
so we need to make it a global parameter. Let's do that and benchmark

```Julia
global lattice_spacing = 0.2
@btime a_big_sum(1000)
#  67.166 μs (3000 allocations: 46.88 KiB)
```

On the other hand, in `a_big_sum_2`, the parameter `lattice_spacing` is define within the
function. It's a keyword argument. So we can pass it to the function and calculate:

```Julia
lattice_spacing = 0.2
@btime a_big_sum_2(1000, lattice_spacing)
#  17.166 μs (1 allocation: 16 bytes)
```

It's four times as fast!! Wow. Why?? Well, it's because the global variable is "far away" from
everything else being used. __Make sure you pass everything that it used in the function
to the function__.

## Locality of calculation

This is helpful for paralellisation. When you do a field theory calculation you want to keep the
calculation _local_. So when you calculate the energy at point `i`, you want this to only depend
on a few other points nearby: `i+1` and `i-1`, and maybe some more. If you do this, your code
should be fast and it should be easy to paralellise. Find out more here.
