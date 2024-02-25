# Smoothing Kernels

Now we are here!

Recall that `q` is defined as $q = r/h$.

$$
\begin{equation}
W(q) = \alpha_D \left( 1 - \frac{q}{2} \right)^4 (2q + 1) \quad \text{for} \quad 0 \leq q \leq 2 \label{eq:wendland_quintic_kernel}
\end{equation}
$$

Where $\alpha_D$ is equal to $\frac{7}{4\pi h^2}$ in 2D and  $\frac{21}{16\pi h^3}$ in 3D. $\eqref{eq:wendland_quintic_kernel}$

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
