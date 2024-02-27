---
layout: subpage
full-width: true
---
# Smoothing Kernels

The smoothing kernel is a corner stone of the SPH methodology. It describes the weighting between material points and thereby affects every single step of a simulation. It is the approximation of the dirac-delta identity and without it, this form of the numerical method would not be achievable. This section seeks to answer the following questions.

1. What is a smoothing kernel
2. What properties does a smoothing kernel have
3. Different kind of smoothing kernels
4. The normalization constant of the smoothing kernel

## Example of a smoothing kernel

An example of a smoothing kernel is the Wendland Quintic kernel as shown in equation $\eqref{eq:wendland_quintic_kernel}$. When working with kernels, it is common to base them on a normalized distance. The operator, $\left\| \left\| () \right\| \right\|$ denotes the Euclidean distance.  $h$ is the smoothing length, a parameter deciding the cut-off of the smoothing kernel. Therefore the normalized distance used in the smoothing kernel, $q$, can be defined as: 

$$
\begin{equation}
q = \frac{\left| \left| \mathbf{x}_i - \mathbf{x}_j \right| \right|}{h}
\label{eq:q}
\end{equation}
$$

Where $\mathbf{x}$ represents the position vector of a material point and subindices $i$ and $j$ denote the particle in focus and the interacting particle representatively. The smoothing kernel will most of the time have a shape similar to a bell-shaped distribution. For the time being the Wendland Quintic kernel given in equation $\eqref{eq:wendland_quintic_kernel}$ is used as an example:  

$$
\begin{equation}
W(q) = \alpha_D \left( 1 - \frac{q}{2} \right)^4 (2q + 1) \quad \text{for} \quad 0 \leq q \leq 2 \label{eq:wendland_quintic_kernel}
\end{equation}
$$

Where $\alpha_D$ is equal to $\frac{7}{4\pi h^2}$ in 2D and  $\frac{21}{16\pi h^3}$ in 3D. To visualize the impact of a kernel a graphic is shown. 

{% figure caption:"Graph of the Wendland kernel showcasing the weighting influence as a function of the normalized distance between material points" label:plot_kernel %}
<div id="KernelPlot" style="width: 680px; height: 400px; margin-left: 200px;">
<script type="text/tikz">
  \pgfmathsetseed{1234}
  \colorlet{purple}{blue!50!red!80!black!80}
  \begin{tikzpicture}[font=\huge]
  % Define radius of the circle
  \def\Radius{5}
  % Draw the big circle
  \draw [thick, dashed, fill=pink, fill opacity=0.5] (0,0) circle (\Radius cm);
  % Draw particles
  \foreach \angle in {0,5,...,360} {
    \node [circle,fill=blue,inner sep=2pt, opacity=0.2] at ({sqrt(rand*0.5+0.5)*\Radius*cos(\angle)}, {sqrt(rand*0.5+0.5)*\Radius*sin(\angle)}) {};
  }
  % Draw boundary annotations
  \draw [thick, dashed] (\Radius,0) arc [start angle=0, end angle=180, radius=\Radius cm];
  \node at ({1.15*\Radius*cos(45)}, {1.15*\Radius*sin(45)}) {$\textrm{d} \cap_S$};
  \node at ({0.75*\Radius*cos(45)}, {0.75*\Radius*sin(45)}) [color=purple] {$\textrm{d} \cap_V$};
  % Draw dashed zero line
  \draw [dashed, thick] (-\Radius,0) -- (\Radius,0);
  % Draw the kernel W
  \draw [thick, ->] (0,0) -- (0,{\Radius*(2/3)}) node [right, fill=none] {$W$};
  % Draw the q vector
  \draw [thick, ->] (0,0) -- ({\Radius/2},0) node [below] {$q$};
  % Draw the kh vector
  \draw [very thick, ->, purple] (0,0) -- ({\Radius*cos(-25)}, {-\Radius*sin(25)}) node [right,color=purple] {$kh$};
  % Create nodes i and j with names for referencing
  \node [circle, fill=green, inner sep=2pt] (i) at (0,0) {}; % Name this node (i)
  \node [circle, fill=yellow, inner sep=2pt] (j) at (-\Radius*0.75,-\Radius*0.25) {}; % Name this node (j)
  % Label for node i
  \node [label=below:$i$, inner sep=2pt] at (i) {};
  % Label for node j
  \node [label=left:$j$, inner sep=2pt] at (j) {};
  % Draw the xi - xj vector using the named nodes
  \draw [very thick, <->] (j) -- (i) node [midway, sloped, below] {$\|\mathbf{x}_i$ - $\mathbf{x}_j\|$};
  % Draw Wendland quintic kernel, aD = 2
  \draw [thick, domain=0:2, smooth, variable=\q, -] plot ({\q*\Radius/2}, {2*pow(1-\q/2,4)*(2*\q+1)});
  \draw [thick, domain=0:2, smooth, variable=\q, -] plot ({-\q*\Radius/2}, {2*pow(1-\q/2,4)*(2*\q+1)});
\end{tikzpicture}
</script>
</div>
{% endfigure %}

As illustrated in {% figref plot_kernel %}, the kernel weighting functions exhibit a monotonically decreasing trend with respect to the distance parameter $q$, which ranges from 0 to 2. This decrement in weighting is congruent with the physical intuition that a material point in closer proximity should exert a more significant influence than one that is more distant. The Euclidean metric employed to compute $q$ encapsulates this spatial relationship between interacting material points. In reality material points at extreme distances can still affect each other very slightly. This can be shown through the Gaussian kernel, which goes towards zero as $q$ goes towards infinity. From a numerical modelling perspective this is not attractive, which is why numerical kernels mimicking the Gaussian kernel, but with a finite cut-off is preferred, for numerical simulation speed.

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

Where $\mathbb{R}^d$ is the dimensions. Notice the succint difference between $W$ and $w$ in the equation. 


## Calculating the normalization constant

This is where the kernel normalization constant, $\alpha_D$ comes in since it ensures that for any choice of positive $h$ the unity condition is fulfilled. To solve for the kernel normalization constants, the following assumptions are taken:

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

```python
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
    if   dimensionality == 1:
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
Which is the correct values for the Wendland Quintic kernel according to litterature. 

<!--
<script type="text/tikz">
\begin{tikzpicture}[font=\huge]
  % Define radius of the circles
  \def\OuterRadius{3}
  \def\InnerRadius{1}

  \draw [draw=none, fill=gray, fill opacity=0.05] (1.85*-\OuterRadius,1.85*-\OuterRadius) rectangle (1.85*\OuterRadius,1.85*\OuterRadius);
  \draw [dashed] (1.85*-\OuterRadius,1.85*-\OuterRadius) rectangle (1.85*\OuterRadius,1.85*\OuterRadius);

  % Draw the outer circle with a dashed border
  \draw [thick, dashed, fill=pink, fill opacity=0.5] (0,0) circle (\OuterRadius);

  % Draw the inner square with a gradient
  \draw [draw=none, fill=orange, fill opacity=0.5] (-\InnerRadius,-\InnerRadius) rectangle (\InnerRadius,\InnerRadius);
  \draw [dashed] (-\InnerRadius,-\InnerRadius) rectangle (\InnerRadius,\InnerRadius);

  % Create nodes i and j with names for referencing
  \node [circle, fill=green, inner sep=2pt] (i) at (0,0) {}; % Name this node (i)
  % Label for node i
  \node [label=below:$i$, inner sep=2pt] at (i) {};
  \node [label=above:$A(\mathbf{x})_i$, inner sep=2pt] at (i) {};

  \node [circle, fill=none, inner sep=2pt, below] (origin) at (-5,-5) {}; 
  \draw [very thick, ->] (-5,-5) -- (0,0) node [midway, above, sloped, fill=none] {$\mathbf{x}$};
  \draw [very thick, ->] (-5,-5) -- (-3,-5) node [midway, above, sloped] {};
  \draw [very thick, ->] (-5,-5) -- (-5,-3) node [midway, above, sloped] {};
  \node [label=below:Origin, inner sep=12pt,] at (origin) {};

  % Draw volume labels
  \node at ({2*\OuterRadius*cos(45)}, {2*\OuterRadius*sin(45)})  {$\cap$};
  \node at ({1.25*\OuterRadius*cos(45)}, {1.25*\OuterRadius*sin(45)}) {$\textrm{d} \cap_S$};
  \node at ({0.80*\OuterRadius*cos(45)}, {0.80*\OuterRadius*sin(45)}) [color=purple] {$\textrm{d} \cap_V$};
  \node at ({1.75*\InnerRadius}, 0) [color=orange] {$\textrm{d} \cap_i$};

\end{tikzpicture}
</script>
-->

### Comparison of kernels

<!-- Plot container -->
{% figure caption:"Comparison of kernels showcasing the weighting influence as a function of the normalized distance between material points. The normalization constant is artificially set to one for all cases, to visually the function flow. Since all of them have unique normalization constants, a comparison using an actual specific value of the smoothing length, would be more accurate." label:plot_kernelcomparison %}
<div id="KernelComparisonPlot" style="width: 680px; height: 400px; margin: auto;"></div>
<script>
    // Define the Gaussian kernel function
function gaussianKernel(q, alphaD) {
    return alphaD * Math.exp(-Math.pow(q, 2));
}
// Define the Wendland Quintic kernel function
function wendlandQuinticKernel(q, alphaD) {
    return alphaD * Math.pow((1 - q / 2), 4) * (2 * q + 1);
}
// Define the Cubic spline kernel function
function cubicSplineKernel(q, alphaD) {
    if (q >= 0 && q <= 1) {
        return alphaD * (1 - 1.5 * q * q + 0.75 * q * q * q);
    } else if (q > 1 && q <= 2) {
        return alphaD * 0.25 * Math.pow((2 - q), 3);
    } else {
        return 0;
    }
}
// Define the function to plot all kernels
function plotKernels(alphaD, divId) {
    var qValues = [];
    var gaussianValues = [];
    var wendlandValues = [];
    var cubicValues = [];
    for (var q = 0; q <= 2; q += 0.01) {
        qValues.push(q);
        gaussianValues.push(gaussianKernel(q, alphaD));
        wendlandValues.push(wendlandQuinticKernel(q, alphaD));
        cubicValues.push(cubicSplineKernel(q, alphaD));
    }
    var trace1 = {
        x: qValues,
        y: gaussianValues,
        type: 'scatter',
        mode: 'lines',
        name: 'Gaussian',
        line: { color: 'blue', width: 2 }
    };
    var trace2 = {
        x: qValues,
        y: wendlandValues,
        type: 'scatter',
        mode: 'lines',
        name: 'Wendland Quintic',
        line: { color: 'red', width: 2 }
    };
    var trace3 = {
        x: qValues,
        y: cubicValues,
        type: 'scatter',
        mode: 'lines',
        name: 'Cubic Spline',
        line: { color: 'green', width: 2 }
    };
    var layout = {
        title: 'Comparison of Kernel Functions',
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
            zeroline: true,
            range: [0, 2]
        },
        yaxis: {
            title: 'W(q)',
            showgrid: false
        },
        legend: {
            x: 1,
            xanchor: 'right',
            y: 1
        }
    };
    var data = [trace1, trace2, trace3];
    Plotly.newPlot(divId, data, layout, { displayModeBar: true, responsive: true });
}
// Call the plotKernels function to plot all three kernels on one graph
plotKernels(1, 'KernelComparisonPlot');
</script>
{% endfigure %}

{% cite ruby --file papers%}