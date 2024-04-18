# Gradient-based Optimization methods for functionals

## The gradient flow relaxation method
Consider a general nonlinear (scalar) field theory with topologically non-trivial configurations. Static solutions are given by field configurations $\Phi_s=(\phi^1,\cdots,\phi^n)$ that make the (static) energy functional 

$$
E[\Phi]=\frac{1}{2} \int\left[h_{a b}(\Phi) \partial_i \phi^a(x) \partial^i \phi^b(x)+V\left(\Phi, \partial_i \Phi\right)\right] d^d x \equiv \int \varepsilon(\Phi) d^d x 
$$

stationary under first variations, i.e. 

$$ \frac{\delta E[\Phi]}{\delta \phi^a}\bigg\rvert_{\Phi_s}=0. $$

Starting from an arbitrary initial configuration $\Phi^{(i)}$ which, in general, will *not* be a static solution of the system, we want to find a static solution within the same homotopy class as $\Phi^{(i)}$. The gradient flow method allows to do so by ``relaxing'' the input field configuration to the true static solution. This is achieved by locally evolving the field in the direction of maximum (local) variation of the energy functional, i.e. the direction of the gradient of the energy density with respect to the field variables.
Indeed, let $\Phi^{(i)}$ be the input configuration. Since it will not be in general a static solution, the first variation of the static energy density in the direction of $\delta\Phi$ will have the form

$$
    \delta E [\Phi^{(i)};\delta\Phi]=\frac{d}{d\epsilon}E[\Phi+\epsilon \delta\Phi]\bigg\rvert_{\epsilon=0}=\int d^dx F_a[\Phi^{(i)}]\delta \phi^a+ \text{B. T.}\neq 0
$$

where B.T. stands for boundary terms, and

$$
    F_a[\Phi]=\frac{\delta E[\Phi]}{\delta \phi^a}=\frac{\partial\varepsilon}{\partial \phi^a}-\partial_ i\frac{\partial\varepsilon}{\partial\partial_ i\phi ^a}
$$

is the *functional derivative* of $E$. The expression above is the generalization of the concept of directional derivative to functionals, and hence the functional derivative is the generalization of the gradient to infinite dimensions.


Considering that the variations are taken over field configurations with fixed boundary conditions, the boundary terms vanish identically after integration. The Gradient flow relaxation method may then be implemented iteratively by changing at each iteration the field configuration as:

$$
    \phi_a^{(i+1)}=\phi_a^{(i)}-\gamma F_a[\Phi^{(i)}],
$$

where $\gamma\in\mathbb{R}_+$ is the **update parameter**. The previous equation is the discrete analogue of the following flow equation in the limit $\gamma\rightarrow 0$:

$$
 \frac{d}{d\gamma}\phi_a(x,\gamma)=-F_a[\Phi(x,\gamma)],\tag{1}
$$

where now $\gamma$ plays the role of an ancillary time parameter. The evolution along  $\gamma$ describes the relaxation of the initial configuration to a static solution, in the sense that 

$$
    E[\Phi^{(i+1)}]\leq E[\Phi^{(i)}].
$$

Since the gradient flow $(1)$ describes a smooth transformation of the field, the final and initial field configurations will (in principle) lay in the same homotopy class of maps. Therefore, we may obtain solitonic solutions with different topological charge by using field configurations with different degree as input ansatz. Of course, for the numerical implementation it is necessary to discretize such transformation, into a discontinuous change on each iteration. One then must be careful with the values of the parameters that enter in the discretized version of the flow equation, so that these discrete steps do not involve a transition between homotopy classes.

### Example: 1 dimensional kink

Consider the $1+1$ dimensional $\phi^4$ model, a nonlinear model described by the Lagrangian

$$
    \mathcal{L}=\frac 1 2 \partial_\mu\phi\partial^\mu\phi - U(\phi).
$$

where $U(\phi)=(\phi^2-1)^2$ is a potential term. This potential has two vacua, namely, $\phi=\pm1$, so that the vacuum manifold is given by the $0-sphere$: $V=S^{0}=\\{-1,1\\}$.

On the other hand, the static energy of this model is given by

$$
    E=\frac{1}{2}\int[ -(\partial_x\phi)^2+U(\phi)]dx \tag{3}
$$

so that static solutions will correspond to field configurations minimizing $(3)$ subject to some boundary conditions imposed at spatial infinity. In this case, these configurations define maps at spatial infinity 

$$
    \phi^\infty:\\{-\infty,\infty\\}\rightarrow \\{+1,-1\\}
$$

depending on the chosen boundary conditions. Such maps are characterized by the fundamental homotopy group $\pi_0(S^0)$, which basically measures the number of path-connected components of $S^0$. Thus, there are four topologically different field configurations: two of them correspond to the topologically trivial vacua, the other ones, to topological solitons --- the *kink*, which interpolates between the $+1$ and $-1$ vacua, and the *antikink*, which goes from $-1$ to $+1$.

In this simple case, the gradient flow equation looks like

$$
\frac{d}{d\gamma}\phi(x,\gamma)=-\gamma F[\phi],\qquad F[\phi]=\frac{\partial^2\phi}{\partial x^2}+\frac{\partial U}{\partial\phi}
$$

### Numerical implementation

A simple version of the gradient flow algorithm starts by defining a discrete lattice with a given number of sites, $n$. We initialize other parameters such as the spatial step and the value of the update parameter $\gamma$.

````{tab-set-code}

```{code-block} Python
n = 101 #number of points in the interval
h = 0.05 #space step
f=np.zeros(n)             # Initialize field variable
gamma=1.*pow(10,-3);      # This is the update parameter. One has to play a little bit to find optimal values. If it is too high, it won't converge, and if it is too low, it will but slowly.
precision = 0.00001       # This tells us when to stop the algorithm
max_iters = 100000        # maximum number of iterations

```

```{code-block} Julia
TBU
```

````


Then, we define auxiliary functions that determine the (second) spatial derivatives of the field at a site and the derivative of the potential with respect to the field.
Let's call them `get_second_derivative` and `dV`.  Then, we just compute the variation of the field at each site.

The code to make this _should_ look like

```
ddphi = get_second_derivative(phi)
for i in range(n):
    varphi[i] -= ddphi[i] - dV(phi[i])
```

here `for i in range(n)` is a _for loop_.  

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
Then, the gradient flow algorithm should look like this

```
############ GRADIENT FLOW ALGORITHM ###############

#Set initial conditions:
f = line(xmax) #set linear configuration as initial condition
varf=np.zeros(n) #initialize varf
printcounter = 0 #initialize print counter
printing = 100; #define the printing frequency in terms of number of iterations
iters = 0        #initialize iteration counter

while deltaE > precision and iters < max_iters:
    energy0=energy(f); #Store current energy value in energy0 
    
    #Calculating and storing the variation at each site:
    
    for j in range (1,n-1) :       #( We do not vary the field at the boundary points)
        varf[j]=var_phi(j,f)
    # Implementing the variation of the field configuration:
    for j in range (1,n-1) :
        f[j]=f[j]-gamma*varf[j];   #new field configuration
    energyf=energy(f);             #Store energy value of the new field configuration in energyf

    deltaE = abs(energyf - energy0) #Change in Energy
    iters = iters+1 #iteration count

    if (printcounter == printing):    
        print("Iteration",iters,"Energy value is",energyf) #Print iterations
        printcounter = 0
    printcounter += 1
print("The minimum energy configuration corresponds to", energyf)

```
In this code, we stop the gradient flow whenever one of two conditions are met. Either the difference between two consecutive values of energy is lower than a threshold value, given by the `precision' parameter, or the maximum number of iterations is reached.

## Higher dimensions 

Although we have seen an application of Gradient Flow to very a simple one dimensional problem, the power of this method is best employed to solve optimization problems which can not be reduced by symmetry, for example, searching for multi-soliton configurations of a nonlinear field theory in more than one dimension.

### Constrained Gradient Flow.

A complication that arises in multi-dimensional problems as opposed to the one dimensional case is that often the fields whose minimum energy configuration we want to find are subject to some **constraints** $C_\alpha (\Phi)=0$, $\alpha=1,...,m$. Therefore, the problem becomes a *constrained minimization* of the energy functional. The standard approach to procede in such cases is the method of  **Lagrange multipliers**, which basically consists on extending the energy functional to include the constraints:

$$
    \tilde{E}[\Phi]=\frac 1 2\int [h_{ab}( \Phi)\partial_i\phi^a(x)\partial^i\phi^b(x) + V(\Phi)+\lambda^\alpha C_\alpha(\Phi)] d^d x,
$$

in which case, the variation of the modified energy density under field variations from an input configuration $\Phi^{(i)}$ will be given by

$$
    \delta \tilde{\varepsilon} [\Phi^{(i)}]=F_a[\Phi^{(i)}]\delta \phi^a+ \lambda^\alpha\frac{\partial C_\alpha}{\partial \phi^a}[\Phi^{(i)}]\delta \phi^a+\text{B. T.}.
$$

In particular, the (static) on-shell field configurations subject to the constraints ${C_\alpha[\Phi]=0}$ will satisfy the modified Euler-Lagrange equations:

$$
    F_a[\Phi]=- \lambda^\alpha\frac{\partial C_\alpha}{\partial\phi^a}[\Phi].
$$

Usually, one can use the equations of motion, the constraints, and the derivatives of the constraints to solve for the multipliers $\lambda^\alpha$ in terms of the original phase space variables (in this case, the values of the fields and their time derivatives). Once the relations $\lambda^\alpha (\Phi,\partial_i \Phi)$ are found, we will be interested in the first order (constrained) flow equation:

$$
 \frac{d}{d\gamma}\phi_a(x,\gamma)=-F_a[\Phi(x,\gamma)]-\lambda^\alpha \frac{\partial C_\alpha}{\partial\phi ^a}[\Phi(x,\gamma)].
$$

## Numerical implementation


The gradient flow relaxation method can be implemented iteratively by changing at each iteration the field configuration. In the case of three dimensions, one needs to discretize the space by defining a grid of $N_x\times N_y\times N_z$ sites, with $h_{x,y,z}$ the distance between grid points in the $x,y,z$ direction. Thus, in the $n-th$ iteration of the gradient flow, on each grid point, labeled by $[i,j,k]$, ($i,j,k \in [0,N_{x,y,z}]$)  the $a-$th component of the field evolves as

$$
    \phi_a^{(n+1)}[i,j,k]=\phi_a^{(n)}[i,j,k]-\gamma F_a[\Phi^{(n)}[i,j,k]]-\gamma \lambda^\alpha \frac{\partial C_\alpha}{\partial\phi ^a}[\Phi^{(n)}[i,j,k]].
$$

In any number of dimensions, for the flow to converge to a (possibly local) minimum, the update parameter $\gamma$ must be small enough. However, too small values will slow down the convergence.

An important difference with respect to the one dimensional problem is that the value of the topological charge cannot be obtained simply from the value of the field at the boundaries. Instead, the topological degree is obtained via an integral of a certain density over the full space. As a consequence of the space discretisation, this integral will fail to yield an integer number in general. Indeed, if the grid spacing constants $h_i$ are chosen too big, it is likely that the topological charge of the field configuration $\Phi$ obtained after numerical integration over all the grid points will be less than the corresponding to such configuration in the continuous limit $h_{x,y,z}\rightarrow 0$.  Both the number of grid points in each directions and the grid spacing constant will play a role in determining the deviation of the numerically obtained value for the topological charge from the integer value in the continuous limit. Such deviation can allow us to determine whether the parameters chosen for the numerical algorithm are well suited to the problem we are trying to solve.


## Accelerated Gradient Descent
An important issue when considering the gradient flow algorithm is that of convergence. Indeed, the gradient flow method is not guaranteed to converge to a local minimum if the configuration space is not convex
(which, in general, will not be the case for a sufficiently complex field theory), but it can converge to a metastable state (a paradigmatic example of such states in a field theory are the *sphalerons* of an $SU(2)$ Yang Mills theory). 
Furthermore, the fact that the field update is proportional to the functional derivative \eqref{updateGF} may produce slow convergence times when the energy valley in field configuration space is very shallow. Indeed, when the field configuration reaches a point of energy close to the minimum, the gradient can be almost zero, making the next update step too small.

These convergence problems can be alleviated by a modification of the gradient flow algorithm which introduces a ''memory term'' that favours the direction of travelling from previous configurations. The regions with small gradient are then transited faster, like a heavy ball would due to its own inertia. 
The inclusion of a momentum term in the gradient descent algorithm is a standard strategy in functional optimization problems, which is usually denoted as ''heavy ball'' method. The drawback of adding such a term appears when the actual minimum is met, and passed by. 
If the momentum term dominates the evolution, the system may not be able to come back to the desired local minimum, and the system may even not be able to find a local minimum ever.

Using the ''heavy ball'' idea, [Nesterov]([https://iopscience.iop.org/article/10.1088/0951-7715/10/1/002](https://www.mathnet.ru/php/archive.phtml?wshow=paper&jrnid=dan&paperid=46009&option_lang=eng))   was able to find the optimal algorithm that is based on single gradients of the energy functional. 
In such algorithm, the gradient is no longer calculated in the current position. Instead, it is done in the position that the configuration would arrive to with the momentum term alone. This gradient now favors (disfavors) the momentum direction if it was approaching to (moving away from) the minimum
This balance leads the algorithm to convergence.
The iterative pattern is then

$$
    \phi_a^{(i+1)}=\phi_a^{(i)}-\Delta\phi_a^{(i+1)},
$$

where now the actualization of the field depends on the field but also on its previous value:

$$
    \Delta\phi_a^{(i+1)}= p^{(i)} \Delta \phi_a^{(i)}-\gamma \tilde F_a[ \Phi^{(i)}+p^{(i)}\Delta\Phi^{(i)}], \quad \Delta \phi_a^{(0)}=0,
$$

where we have defined

$$
    \tilde F_a[\Phi]=F_a[\Phi]+\lambda^\alpha \frac{\partial C_\alpha}{\partial\phi ^a}[\Phi],
$$

and the momentum parameter $p^{(i)}$, given by

$$
    p^{(i)}=\frac{v^{(i)}}{v^{(i)}+3},
$$

with

$$
    v^{(i+1)}=
    \begin{cases}
    v^{(i)}+1, & {\rm if} \quad E[\Phi^{(i+1)}] < E[\Phi^{(i)}], \\
     0, & {\rm if }\quad E[\Phi^{(i+1)}] > E[\Phi^{(i)}],
    \end{cases}
$$

and $v^{(0)}=0$. 

The minimization algorithm so defined is often called **accelerated gradient flow**. It is accelerated in the sense that the momentum parameter grows with each iteration, but it is brought back to its initial value $(p=0)$ when the energy minimum is passed by.


# Arrested Newton Flow
To be continued...

## Read more

Gradient flow can also be used to generate collective coordinate space. Find out more in [this paper](https://iopscience.iop.org/article/10.1088/0951-7715/10/1/002) by Manton and Merabet.
