<!-- .slide: data-background="#1c1c1c" -->
<div style="margin:auto;max-width:100%;padding:10px;text-align:center;">
<h1 class="r-fit-text">Training Topological Samplers</h1>
<h2 class="r-fit-text">for Lattice Gauge Theories</h2>

# [**Sam Foreman**](mailto://foremans@anl.gov)

### \[`date`\]

[<img align="left" width=10% src="attachments/github.svg">](https://github.com/saforem2/l2hmc-qcd)
[<img align="right" width=30% src="attachments/Argonne_cmyk_white.svg">](https://alcf.anl.gov)

---

<div style="margin:0 0 15 20; text-align: left; float: left; width: 48%; font-size: 0.75em; line-height: 1.2;">

## <div style="color: #3B4CC0;">Critical Slowing Down</div>

- As $\beta\rightarrow\infty$, configurations tend to get stuck, $Q=\text{const}$.

- Or, equivalently, the **tunneling rate** $\delta Q\equiv\left|Q_{i+1} - Q_{i}\right|\rightarrow 0$ as $\beta\rightarrow\infty$

- <div id="note" style="padding-left: 10px; padding-top: 5px; padding-bottom: 6px;">
  	<b>Goal</b>: Draw <i>independent</i> samples from target distribution $p(x)\propto e^{-\beta\,S(x)}$
  </div>

</div>

<div style="margin:0 0 15 0; text-align: left; float: right; width: 48%; font-size: 0.75em;">

![Icon](attachments/charge_freezing-crop.svg)

</div>

---

## Hamiltonian Monte Carlo (HMC)

<div style="font-size: 0.75em; text-align: left;">

- Introduce $v \sim \mathcal{N}(0, \mathbb{I})$ distributed independently of $x$

- Our target density is then:
  $$ p(x, v) = p(x) \cdot p(v) = e^{-\beta S(x)}\cdot e^{-v^{T}v / 2} = e^{-H(x, v)}$$

- We can evolve the joint $\xi = (x, v)$ system using **Hamilton's equations** along $H=\text{const.}:$
  $$ \dot{x} = \frac{\partial H}{\partial v},\quad \dot{v} = -\frac{\partial H}{\partial x}\longrightarrow$$

<div id="left">

- ## Leapfrog Integrator $\Longrightarrow$

  <div id="centerflex">
  <div class="sl-block" style="width: auto; height:auto;">
  <div class="sl-block-content notranslate" style="z-index:15;">
  <div id="note" style="max-width: 100%;padding-right:50px;text-align:left;padding-top:20px;line-height:1.3;">

  <ol>
    <li> $\,\, \tilde{v}\leftarrow v - \varepsilon\cdot \partial_{x} S(x) / 2$</li>
    <li> $\,\, x'\leftarrow x + \varepsilon \tilde{v}$</li>
    <li> $\,\, v' \leftarrow \tilde{v} - \varepsilon\cdot \partial_{x} S(x') / 2$</li>
  </ol>

  </div>
  </div>
  </div>
  </div>

</div>
<div id="right">

- ## Metropolis-Hastings accept/reject
  <div id="note" style="max-width: 95%;padding:auto;padding-right:10px;line-height:1.;">

  `\[\begin{equation}
    x_{i+1}\longleftarrow
    \begin{cases}
    x'\quad\text{w/prob.}\quad A(\xi'|\xi)=\min\left\{1,\frac{p(\xi')}{p(\xi)}\left|\frac{\partial\xi'}{\partial\xi}\right|\right\},\\
    x_{i} \quad\text{w/prob.}\quad 1 - A(\xi'|\xi)
    \end{cases}
    \end{equation}\]`

    </div>

<!-- $$ x_{i+1}\longleftarrow\begin{cases} x'\quad\text{with prob.}\quad A(\xi'|\xi)=\min\left\{1,\frac{p(\xi')}{p(\xi)}\left|\frac{\partial\xi'}{\partial\xi}\right|\right\},\\ -->

</div>

---

## Hamiltonian Monte Carlo

<!-- <div id="centerflex">
<div id="note" style="max-width: 100%;padding-right:20px;text-align:left;padding-top:20px;line-height:1.6;">

<ol>
<li> $\,\, \tilde{v}\leftarrow v - \frac{\varepsilon}{2}\cdot \partial_{x} S(x)$</li>
<li> $\,\, x'\leftarrow x + \varepsilon \tilde{v}$</li>
<li> $\,\, v' \leftarrow \tilde{v} - \frac{\varepsilon}{2}\cdot \partial_{x} S(x')$</li>
</ol>

</div>
</div> -->

![](attachments/hmc-crop.svg)

---

### Networks

<div style="max-width=99%">

![](attachments/net_fns.svg)

</div>

---
	
<div class="sl-block" style="width: auto; height: auto;">
	<div class="sl-block-content notranslate" style="z-index: 15; background-color: rgba(109, 158, 235, 0.35); border-style: solid; border-radius: 5px; border-color: rgb(255, 255, 255); padding: 4px;">
		$p(\xi) = p(x)\cdot p(v)\cdot p(d)$
	</div>
</div>


<style>
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Sofia&effect=fire">
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto">
</style>
