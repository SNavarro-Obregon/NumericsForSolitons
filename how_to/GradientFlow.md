# Gradient Flow

Gradient flow is used to solve the following problem

$$\frac{\partial\phi}{\partial \tau} = -\frac{\delta E}{\delta \phi}$$

Here $\phi(x, \tau)$ is your field.

The code to make this should look like

```
ddphi = get_second_derivative(phi)
for i in range(N):
    phi[i] -= ddphi[i] - dV(phi[i])
```

here `get_second_derivative` is a function. `dV` gives the derivative of your potential 

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
