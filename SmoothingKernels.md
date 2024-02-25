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
