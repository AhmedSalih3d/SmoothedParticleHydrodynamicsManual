---
layout: subpage
full-width: true
---
# Smoothing Kernels

The smoothing kernel is a corner stone of the SPH methodology. It describes the weighting between material points and thereby affects every single step of a simulation. It is the approximation of the dirac-delta identity and without it, this form of the numerical method would not be achievable. This section seeks to answer the following questions.

1. What is a smoothing kernel
2. What properties does a smoothing kernel have
3. Different kind of smoothing kernels
4. Smoothing kernel constant



## Example of a smoothing kernel

An example of a smoothing kernel is the Wendland Quintic kernel as shown in equation $\eqref{eq:wendland_quintic_kernel}$. When working with kernels, it is common to base them on a normalized distance. The operator, $\left\| \left\| () \right\| \right\|$ denotes the Euclidean distance.  $h$ is the smoothing length, a parameter deciding the cut-off of the smoothing kernel. Therefore the normalized distance used in the smoothing kernel, $q$, can be defined as: 

$$
\begin{equation}
q = \left\|  \mathbf{x}_{ij}  \right\| /h. \label{eq:q}
\end{equation}
$$

The smoothing kernel will most of the time have a shape similar to a bell-shaped distribution. For the time being the Wendland Quintic kernel given in equation $\eqref{eq:wendland_quintic_kernel}$ is used as an example:  

$$
\begin{equation}
W(q) = \alpha_D \left( 1 - \frac{q}{2} \right)^4 (2q + 1) \quad \text{for} \quad 0 \leq q \leq 2 \label{eq:wendland_quintic_kernel}
\end{equation}
$$

Where $\alpha_D$ is equal to $\frac{7}{4\pi h^2}$ in 2D and  $\frac{21}{16\pi h^3}$ in 3D. This specific kernel is not defined in 1D, but 1D kernels do exist. For simplicity, taking $\alpha_D = 1$ for now and visualizing it:

<!-- Plot container -->
{% figure caption:"Graph of the Wendland kernel showcasing the weighting influence as a function of the normalized distance between material points" label:plot_wendlandkernel %}
<div id="KernelPlot" style="width: 480px; height: 400px;"></div>
<script>
    // Define kernel function
    function W(q, alphaD) {
        return alphaD * Math.pow((1 - q/2), 4) * (2*q + 1);
    }
    var qValues = [];
    var wValues = [];
    var alphaD  = 1 ; // Normalized to 1
    for(var q = 0; q <= 2; q += 0.01) {
        qValues.push(q);
        wValues.push(W(q, alphaD));
    }
    var trace1 = {
        x: qValues,
        y: wValues,
        type: 'scatter',
        mode: 'lines',
        name: 'W \left( q \right)',
        line: {
            color: 'black',
            width: 2
        }
    };
    var layout = {
                title: 'Plot of W(q)',
                plot_bgcolor: 'white',
                paper_bgcolor: 'white',
                font: {
                    family: 'Serif',
                    size: 18,
                    color: 'black'
                },
                xaxis: {
                    title: 'q',
                    showgrid: false,
                    gridcolor: 'black',
                    zeroline: true,
                    range: [0,2]
                },
                yaxis: {
                    title: 'W(q)',
                    showgrid: false,
                    gridcolor: 'black'
                }
            };
    var data = [trace1];
    Plotly.newPlot('KernelPlot', data, layout, {displayModeBar: true, responsive: true});
</script>
{% endfigure %}

As seen in {% figref plot_wendlandkernel %} as $q$ goes from 0 to 2, the weighting decreases. Since $q$ is the euclidean distance between material points, this makes intuitively sense - it is expected that a particle closer by should be weighted higher than one further away. In reality material points at extreme distances can still affect each other very slightly. This is what can be shown through the Gaussian kernel, which goes towards zero as $q$ goes towards infinity. From a numerical modelling perspective this is not attractive, which is why numerical kernels mimicking the Gaussian kernel, but with a finite cut-off is preferred, for numerical simulation speed.

## Properties of a smoothing kernel

At the end of the day the choice of smoothing kernel is a decision taken "arbitrarily", in the sense that if a few mathematical constraints are upheld, then the smoothing kernel can be "mathematically" justified. In brief:

* As the smoothing length, $h$ approaches zero, the weighting function, $W$ approach the dirac-delta identity. 
* The volume integral of the weighted function in its specified domain should equal unity (1). To ensure that over-weighting is not possible.

In practical terms, since the aim is to simulate physics, some practical considerations should be taking into account. A non-comprehensive list is provided below, specifically for computational fluid dynamics, considering a three dimensional space:

1. Evenly distributed volume support domain
* The spatial volume of the kernel support domain should not differ in the spatial directions 
2. Radial symmetry
* Influences approaching the material points from the same distance but different orientations, should produce the same weighting
3. Compact support
* The kernel should have a finite cut-off for numerical simulation practicalities
4. Positive smoothing length
* The kernel cut-off radius must be positive
4. Constant smoothing length
* The value of $h$ is constant. There is no physical or mathematical reason this *has* to be the case, but it is what is assumed for the following derivations.
5. Monotonically decreasing
* As $q$ gets larger, the weighting should decrease consistently
6. Differentiable
* Simulation of computational fluid dynamics requires derivatives, the weighting function, the basis of the simulation approach must be differentiable, preferably more than twice
7. Positive weighting values
* For all valid values of $q$ the kernel must return a value greater than or equal to zero

For the first point, the "ground truth" is often taken as a spherical support domain for the kernel, i.e. a spherical volume around the material point. In theory a box with equal side lengths would fulfill the criteria as well, but the added complexity does not seem to provide any real benefits. The list of considerations above are not necessarily mathematical. For example it is indeed possible to define a kernel fulfilling the mathematical criterias, but violating some of the items mentionedh here. One example of why these considerations still are of utmost importance, is that when later, the density estimation through the kernel operator will be discussed, it is clear that a kernel violating the criteria of positive weighting values will lead to negative density values! This of course goes against the physics intended to be modelled.

## Different kinds of smoothing kernels

Smoothing kernels are of many different kinds and have each their own benefits and detriments. Benefits and detriments can range between the different necessary kernel cut-off radii, the different levels of differentiability, the different levels of computational performance and so forth. Three types of kernels are shown below. Once again the Wendland quintic kernel is provided, due to its simple formulation in equation $\eqref{eq:wendland_kernel}$, the Gaussian kernel is shown in equation $\eqref{eq:gaussian_kernel}$ and finally the cubic spline as seen in equation $\eqref{eq:cubic_spline_kernel}$.

**1. Gaussian kernel**
$$
\begin{equation}
W(q) = \alpha_D \exp(  -q^2)
\label{eq:gaussian_kernel}
\end{equation}
$$

**2. Wendland Quintic kernel**
$$
\begin{equation}
W(q) = \alpha_D \left( 1 - \frac{q}{2} \right)^4 (2q + 1) \quad \text{for} \quad 0 \leq q \leq 2 
\label{eq:wendland_kernel}
\end{equation}
$$

**3. Cubic spline kernel**
$$
\begin{equation}
W(q) = \alpha_D
\begin{cases}
1 - \frac{3}{2}q^2 + \frac{3}{4}q^3 & \text{for} \quad 0 \leq q \leq 1 \\
\frac{1}{4}(2-q)^3                  & \text{for} \quad 1 < q \leq 2 \\
0                                   & \text{for} \quad q > 2
\end{cases}
\label{eq:cubic_spline_kernel}
\end{equation}
$$

These three types of kernels highlight some of the most common types encountered in literature and simulation software. In purely graphical simulations, where physical accuracy takes a back-seat some other kernels can be found.

As mentioned previously, the kernels must fulfill the unity condition:

$$
\begin{equation}
\int_{\mathbb{R}^d} W(\mathbf{x} - \mathbf{x}',h) \hspace{1mm} dV = \int_{\mathbb{R}^d} \alpha_D  \hspace{1mm} w(\mathbf{x} - \mathbf{x}',h) \hspace{1mm} dV = 1
\label{eq:unity_condition}
\end{equation}
$$

Where $\mathbb{R}^d$ is the dimensions. Notice the succint difference between $W$ and $w$ in the equation. This is where the kernel normalization constant, $\alpha_D$ comes in since it ensures that for any choice of positive $h$ the unity condition is fulfilled. To solve for the kernel normalization constants, the following assumptions are taken:

1. A spherical kernel support volume
* Therefore integration is in 1D over a line, in 2D over a circular area and in 3D over a spherical volume
2. Radial symmetry (even function)
* The integration must be done over the whole volume. One way of doing that is described here. Using the kernel cut-off radius, $H = \kappa h$, where $\kappa$ is often taken as 2, the integration range is from 0 up to $H$. This is due to the circular/spherical nature of the integration, imagine revolution integral to encompass the volume. The Gaussian kernel is a special case with limits of integration from $-\infty$ to $+\infty$.

With these assumptions in place it is possible to solve for the correct value of $\alpha_D$ using the following generalized integral:

$$
\begin{equation}
\alpha_D  = \frac{1}{\int_{\mathbb{R}^d} w(\mathbf{x} - \mathbf{x}',h) \hspace{1mm} dV}
\label{eq:solve_for_ad}
\end{equation}
$$

Where the first step of deriving equation $\eqref{eq:solve_for_ad}$ is the assumption of $h$ being constant and thereby able to move it out from the inner part of the integration in equation $\eqref{eq:unity_condition}$. The volume element, $dV$ changes based on dimensionality, for example for 2D, using polar coordinates, which is definitely the simplest for these kernels, $dV$ would be $r dr d\theta$, where r is the "radius", distance between a point and kernel origin. Basically, $r = \mathbf{x} - \mathbf{x}'$, when it has been concretized.

```py
from sympy import symbols, integrate, pi, sin
def calculate_normalization_constant(w, dimensionality, kappa=2):
    # Define the symbols
    r, h, theta, phi = symbols('r h theta phi', real=True, positive=True)
    alpha_D = symbols('alpha_D', real=True, positive=True)  # Normalization constant
    
    # Define the volume elements for different dimensions
    volume_elements = {
        1: 1,             # Line
        2: r,             # Circle (polar coordinates)
        3: r**2*sin(phi)  # Sphere (spherical coordinates)
    }
    # Define the integration limits for different dimensions
    integration_limits = {
        1: (r, -kappa*h, kappa*h),                              # Line
        2: ((r, 0, kappa*h), (theta, 0, 2*pi)),                 # Circle
        3: ((r, 0, kappa*h), (theta, 0, 2*pi), (phi, 0, pi))    # Sphere
    }
    # Select the appropriate volume element and limits for the given dimensionality
    volume_element = volume_elements[dimensionality]
    limits         = integration_limits[dimensionality]
    # Perform the integration
    if dimensionality == 1:
        integral = integrate(w.subs(q,r/h) * volume_element, limits)
    elif dimensionality == 2:
        integral = integrate(w.subs(q,r/h) * volume_element, *limits) 
    elif dimensionality == 3:
        integral = integrate(w.subs(q,r/h) * volume_element, *limits) 
    # Solve for the normalization constant alpha_D
    alpha_D_value = 1 / integral
    return alpha_D_value.simplify()
# Define q
q = symbols('q', real=True, positive=True)
# Define the Wendland Quintic kernel function w(q)
w = (1 - q / 2)**4 * (2 * q + 1)

# Calculate the normalization constant for 2D
alpha_D_2d = calculate_normalization_constant(w, 2, 2)

# Calculate the normalization constant for 3D
alpha_D_3d = calculate_normalization_constant(w, 3, 2)

alpha_D_2d, alpha_D_3d
```

When this code is ran the $\alpha_D$ values for the Wendland quintic kernel is output as:

```python
alpha_D_2d = (7/(4*pi*h**2)
alpha_D_3d = 21/(16*pi*h**3))
```

{% cite ruby --file papers%}