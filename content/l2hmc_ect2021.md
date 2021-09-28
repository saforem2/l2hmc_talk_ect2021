<link rel="stylesheet" src="../themes/white.css">
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Sofia&effect=fire">
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto">

<!-- .slide: data-background="#1c1c1c" -->
<h2 class="r-fit-text">Training Topological Samplers</h2>

## for Lattice Gauge Theories

### <div style="color: #00CCFF">[**Sam Foreman**](mailto://foremans@anl.gov)</div>

##### 09/29/2021

[<img align="left" width=10% src="attachments/github.svg">](https://github.com/saforem2/l2hmc-qcd)
[<img align="right" width=30% src="attachments/Argonne_cmyk_white.svg">](https://alcf.anl.gov)

---

# Generating Gauge Configurations

---

<!-- <div style="text-align: left; width: 48%; font-size: 0.75em; line-height: 1.2;"> -->
<div id="left" style="font-size: 0.75em;">

## <div style="color: #3B4CC0;">Critical Slowing Down</div>

- As $\beta\rightarrow\infty$, configurations tend to get stuck at $Q=\text{const}$.

- Equivalently, the _**tunneling rate**_: $\delta Q\equiv\left|Q_{i+1} - Q_{i}\right|\rightarrow 0$ as $\beta\rightarrow\infty$

- <div id="note" style="padding-left: 10px; padding-top: 5px; padding-bottom: 6px;">
  	<b>Goal</b>: Draw <i>independent</i> samples from target distribution $p(x)\propto e^{-\beta\,S(x)}$
  </div>

</div>

<div style="margin:0 0 15 0; text-align: left; float: right; width: 48%; font-size: 0.75em; max-height: 95%;">

![charge_freezing](attachments/charge_freezing-crop.svg)

</div>

---

## Hamiltonian Monte Carlo (HMC)
<div style="font-size: 0.6em;text-align: left;">

- Introduce $v \sim \mathcal{N}(0, \mathbb{I})$ distributed independently of $x$

- Our target density is then:
  $$ p(x, v) = p(x) \cdot p(v) = e^{-\beta S(x)}\cdot e^{-v^{T}v / 2} = e^{-H(x, v)}$$

- We can evolve the joint $\xi = (x, v)$ system using **Hamilton's equations** along $H=\text{const.}:$
  $$ \dot{x} = \frac{\partial H}{\partial v},\quad \dot{v} = -\frac{\partial H}{\partial x}$$

<div id="left">

- ## Leapfrog Integrator

  <div id="note" style="margin:auto; text-align:left; float: left;padding-top:20px; padding-right: 20px; line-height:1.75;font-size:1.2em;">
  <ol>
  <li> $\,\, \tilde{v}\leftarrow v - \frac{\varepsilon}{2}\cdot \partial_{x} S(x)$</li>
  <div id="bright"><li> $\,\, x'\leftarrow x + \varepsilon \tilde{v}$</li> </div>
  <li> $\,\, v' \leftarrow \tilde{v} - \frac{\varepsilon}{2}\cdot \partial_{x} S(x')$</li>
  </div>

</div>

</div>

<div id="right" style="font-size:0.6em;">

<!-- `\begin{equation} -->
<div id="note" style="margin:auto; padding:5px; text-align:left; float: right; background-color:#efefef; border:none;">

### <u>Accept / Reject:</u>
<div style="padding-left:10px;">

<div id="bright">
$x_{i+1}\leftarrow\begin{cases}
x'\text{ w/ prob. } A(\xi'|\xi),\\
x_{i} \text{ w/ prob. }1 - A(\xi'|\xi)
\end{cases}$
</div>

and  `$A(\xi'|\xi)=\min\left\{1,\frac{p(\xi')}{p(\xi)}\left|\frac{\partial\xi'}{\partial\xi}\right|\right\}$`
<!-- \end{cases}\end{equation}` -->

</div>
</div>

</div>

---

**Metropolis-Hastings accept/reject**
  `\[\begin{equation}
  x_{i+1}\leftarrow
  \begin{cases}
  x'\quad\text{w/prob.}\quad A(\xi'|\xi)=\min\left\{1,\frac{p(\xi')}{p(\xi)}\left|\frac{\partial\xi'}{\partial\xi}\right|\right\},\\
      x_{i} \quad\text{w/prob.}\quad 1 - A(\xi'|\xi)
  \end{cases}
  \end{equation}\]`

---

![](attachments/pdfs/l2hmc_slides_svg/slides3.svg)  <!-- element.width:100% -->

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
</style>
