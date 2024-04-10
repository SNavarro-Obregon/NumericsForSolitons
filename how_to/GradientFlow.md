# Gradient Flow

## Theory

**Gradient flow** is used to solve the following problem

$$\frac{\partial\phi}{\partial \tau} = -\frac{\delta E}{\delta \phi}$$

Here $\phi(x, \tau)$ is your field.

## Implementation

The code to make this _should_ look like

```
ddphi = get_second_derivative(phi)
for i in range(N):
    phi[i] -= ddphi[i] - dV(phi[i])
```

here `for i in range(N)` is a _for loop_. `dV` gives the derivative of your potential 

````{tab-set-code}

```{code-block} Python
def dV(phi):
    return 4*phi*(1-phi**2)
```

```{code-block} Julia
function dV(phi) = 4*phi*(1-phi^2)
```

````

You could define this using automatic differentiation...

````{tab-set-code}

```{code-block} Python
def dV(phi):
    blah blah
```

```{code-block} Julia
function dV(phi) = i'm not sure
```

````

## Read more

Gradient flow can also be used to generate collective coordinate space. Find out more in [this paper](https://iopscience.iop.org/article/10.1088/0951-7715/10/1/002) by Manton and Merabet.
