# Smoothing Kernels

The smoothing kernel is a corner stone of the SPH methodology. It describes the weighting between material points and thereby affects every single step of a simulation. It is the approximation of the dirac-delta identity and without it, this form of the numerical method would not be achievable. This section seeks to answer the following questions.

1. What is a smoothing kernel
2. What properties does a smoothing kernel have


## Example of a smoothing kernel

An example of a smoothing kernel is the Wendland Quintic kernel as shown in equation $\eqref{eq:wendland_quintic_kernel}$. When working with kernels, it is common to base them on a normalized distance. The operator, $\left\| \left\| () \right\| \right\|$ denotes the Euclidean distance given by, $c = sqrt(a^2+b^2)$.  $h$ is the smoothing length, a parameter deciding the cut-off of the smoothing kernel. Therefore the normalized distance used in the smoothing kernel, $q$, can be defined as: 

$$
\begin{equation}
q = \left\|  \mathbf{x}_{ij}  \right\| /h. \label{eq:q}
\end{equation}
$$

The smoothing kernel will most of the time have a shape similar to a Gaussian distribution. For the time being the Wendland Quintic kernel given in equation $\eqref{eq:wendland_quintic_kernel}$ is used as an example:  

$$
\begin{equation}
W(q) = \alpha_D \left( 1 - \frac{q}{2} \right)^4 (2q + 1) \quad \text{for} \quad 0 \leq q \leq 2 \label{eq:wendland_quintic_kernel}
\end{equation}
$$

Where $\alpha_D$ is equal to $\frac{7}{4\pi h^2}$ in 2D and  $\frac{21}{16\pi h^3}$ in 3D. This specific kernel is not defined in 1D, but 1D kernels do exist.

<!-- Plot container -->
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
        name: '$W \left( q \right)$',
        line: {
            color: 'black',
            width: 2
        }
    };
    var layout = {
                title: 'Plot of W(q)',
                plot_bgcolor: 'white',
                paper_bgcolor: 'white',
                showlegend: 'true',
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
