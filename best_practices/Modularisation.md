# Modularisation

Modularisation says ``PUT EVERYTHING IN A FUNCTION!!!". And a function should do one thing
and do that thing well. It's key to the Don't Repeat Yourself (DRY) mindset. Do you have two functions which can take a derivative? That's one too many! So there should be one simple
function for each task. A side-tip: that function's name should be so good that you don't
need comments.

We'll examine this idea by _refactoring_ some code. Refactoring is when you take a large, ugly
function and turn it into several smaller functions. Suppose we're trying to calculate the energy
of a $\phi^4$ kink. Currently, we have two functions. One returns the total energy and the other
returns the energy density. Both are useful: sometimes you need the energy density and sometimes
you need the energy. We're using a kink _object_ (learn more here):

``` Julia
function calculate_energy(kink::Kink)

    total_energy = 0.0

    for i in 2:kink.lp-1
        dkink = (kink.field[i+1] - kink.field[i-1])/(2*kink.ls)
        total_energy += 0.5*dkink^2 + 0.5*(kink.field[i]^2 - 1)^2
    end

    return total_energy*kink.ls

end

function calculate_energy_density(kink::Kink)

    energy_density = zeros(kink.lp)

    for i in 2:kink.lp-1
        dkink = (kink.field[i+1] - kink.field[i-1])/(2*kink.ls)
        energy_density[i] = 0.5*dkink^2 + 0.5*(kink.field[i]^2 - 1)^2
    end

    return energy_density

end
```

There are several problems with this code: 2nd order derivatives aren't very accurate and we're
ignoring boundary points. Learn more in the derivatives and boundary conditions sections.

Ok! Let's refactor. We're repeating ourselves a lot. Let's start by defining kinetic and potential functions, and a function to calculate the derivative:

```
function potential_energy(phi)
    return 0.5*(1-phi^2)^2
end

function kinetic_energy(dphi)
    return 0.5*dphi^2
end

function dx(kink,i)
    return (kink.field[i+1] - kink.field[i-1])/(2*kink.ls)
end
```

and update our energy functions too

```
function calculate_energy(kink::Kink)

    total_energy = 0.0

    for i in 2:kink.lp-1
        dkink = dx(kink, i)
        total_energy += derivative_energy(dkink) + potential_energy(kink.field[i])
    end

    return total_energy*kink.ls

end

function calculate_energy_density(kink::Kink)

    energy_density = zeros(kink.lp)

    for i in 2:kink.lp-1
        dkink = dx(kink, i)
        energy_density[i] += derivative_energy(dkink) + potential_energy(kink.field[i])
    end

    return energy_density

end
```

So, what's nice about this? First, it's easier to read the energy functions. Second, suppose 
you want to now study the $\phi^6$ model. Now you only need
to change one thing - the `potential_energy` function! So, it's more likely your two energy functions will be consistent. But
having two energy functions is a bit of a pain. So let's turn them into one. We'll do this by
adding a boolean keyword argument to `calculate_energy` called `density`. If this is true, we'll
return a density and if it's false, we'll return the energy. To make this work, we'll
change things a little

```
function calculate_energy(kink::Kink; density=false)

    energy_density = zeros(kink.lp)

    for i in 2:kink.lp-1
        dkink = dx(kink, i)
        energy_density[i] = derivative_energy(dkink) + potential_energy(kink.field[i])
    end

    # note: `if density` is shorthand for `if density == true`
    if density
        return total_energy*kink.ls
    else
        return energy_density
    end

end
```

Nice! We now have a 2-for-1 function. This will give us the energy or the energy density
depending on what we feed it. In some ways this is more complicated than before: 
we have a more complicated function (bad) but we only have one place where we calculate
the energy (good). Is this the best function? Should you make a `get_energy_at_point`
function? Or go back to what it looked like before? Of course, it's up to you. But
hopefully this guide has given you a feeling for what you _might_ want to do.

## A quick note on speed.

On Julia, we can benchmark functions very easily. In our refactoring, it didn't
feel like we changed much. In fact, our final solution looks _worse_ speed wise when we
calculate the energy, because we make an energy density array and don't use it.

Here's my output when I benchmark the original functions on a 10,000 point kink:

```
my_kink = Kink(10000,0.2)
my_kink.field = rand(10000);
@btime calculate_energy(my_kink)
# 927.166 μs (58461 allocations: 913.45 KiB)
```

almost a millisecond.  Now with the new refactored functions. Drumroll...

```
@btime calculate_energy(my_kink, density=false)
# 911.792 μs (58460 allocations: 991.58 KiB)
```

...it's the same(ish). It turns out that making an array of zeros doesn't affect
things much. For very large objects this can be a problem for memory, but we're
a far way off this first. Just note that your instinct about what is slow and
what is fast in code might be very wrong. Mine is! The only way to find out is to
check.