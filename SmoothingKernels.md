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
<div id="myPlot" style="width:100%;max-width:600px;height:400px;"></div>

<script>
  var data = [{
    x: [1, 2, 3, 4],
    y: [10, 11, 12, 13],
    type: 'scatter'
  }];

  Plotly.newPlot('myPlot', data);
</script>

<!-- Plot container -->
<div id="myDiv" style="width: 480px; height: 400px;"></div>
<script>
// Define the function W(q)
function W(q, alphaD) {
    return alphaD * Math.pow((1 - q/2), 4) * (2*q + 1);
}

// Generate data
var qValues = [];
var wValues = [];
var alphaD = 7 / (4 * Math.PI); // For 2D, as an example
for(var q = 0; q <= 2; q += 0.01) {
    qValues.push(q);
    wValues.push(W(q, alphaD));
}

// Create the plot
var trace1 = {
    x: qValues,
    y: wValues,
    type: 'scatter',
    mode: 'lines',
    name: 'W(q)',
    line: {
        color: 'blue',
        width: 2
    }
};

var layout = {
    title: 'Plot of W(q)',
    xaxis: {
        title: 'q',
        showgrid: true,
        zeroline: true
    },
    yaxis: {
        title: '\\(W(q)\\)',
        showline: true
    },
    margin: {
        t: 40
    },
    plot_bgcolor: "#e5ecf6",
    paper_bgcolor: "#e5ecf6"
};

var data = [trace1];

Plotly.newPlot('myDiv', data, layout, {displayModeBar: true, responsive: true});
</script>
