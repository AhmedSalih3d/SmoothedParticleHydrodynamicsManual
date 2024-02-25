# Smoothing Kernels

Now we are here!

Recall that `q` is defined as $q = r/h$.

$$
\begin{equation}
W(q) = \alpha_D \left( 1 - \frac{q}{2} \right)^4 (2q + 1) \quad \text{for} \quad 0 \leq q \leq 2 \label{eq:wendland_quintic_kernel}
\end{equation}
$$

Where $\alpha_D$ is equal to $\frac{7}{4\pi h^2}$ in 2D and  $\frac{21}{16\pi h^3}$ in 3D. $\eqref{eq:wendland_quintic_kernel}$

<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
<div id="cd52e831-399a-403d-9bb2-0c56214b1d38" style="height: 100%; width: 100%;" class="plotly-graph-div"></div>
<script type="text/javascript">window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("cd52e831-399a-403d-9bb2-0c56214b1d38", [{"type": "pie", "values": [4500, 2500, 1053, 500], "labels": ["Oxygen", "Hydrogen", "Carbon_Dioxide", "Nitrogen"]}], {}, {"linkText": "Export to plot.ly", "showLink": true})</script>
