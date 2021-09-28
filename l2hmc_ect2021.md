---
title: "l2hmc-qcd @ ECT 2021"
theme: simple
highlightTheme: github
---

<!-- .slide: data-background="#1c1c1c" -->
<!-- <h2 class="r-fit-text">Training Topological Samplers</h2> -->
## <h2 class="r-fit-text">Training Topological Samplers</h2>
### for Lattice Gauge Theories
### <div id="bright">[**Sam Foreman**](mailto://foremans@anl.gov)</div>
##### 09/29/2021

[<img align="left" width=10% src="assets/github.svg">](https://github.com/saforem2/l2hmc-qcd)
[<img align="right" width=30% src="assets/Argonne_cmyk_white.svg">](https://alcf.anl.gov)

---

# Generating Gauge Configurations

- In order to calculate physical quantities of interest, we 

---

<div id="left" style="font-size: 0.75em;">

## <div style="color: #3B4CC0;">Critical Slowing Down</div>

- As $\beta\rightarrow\infty$, configurations tend to get stuck at $Q=\text{const}$.

- Equivalently, the _**tunneling rate**_: $\delta Q\equiv\left|Q_{i+1} - Q_{i}\right|\rightarrow 0$ as $\beta\rightarrow\infty$

- <div id="note" style="padding-left: 10px; padding-top: 5px; padding-bottom: 6px;">
  	<b>Goal</b>: Draw <i>independent</i> samples from target distribution $p(x)\propto e^{-\beta\,S(x)}$
  </div>

</div>

<div id="right">

![charge_freezing](assets/charge_freezing-crop.svg)

</div>

---

## Hamiltonian Monte Carlo (HMC)

- Introduce $v \sim \mathcal{N}(0, \mathbb{1})$ distributed independently of $x$

- Our target density is then:
  $$ p(x, v) = p(x) \cdot p(v) = e^{-\beta S(x)}\cdot e^{-v^{T}v / 2} = e^{-H(x, v)}$$

- We can evolve the joint $\xi = (x, v)$ system using **Hamiltons equations** along $H=\text{const.}:$
  <div id="note">

  $$ \dot{x} = \frac{\partial H}{\partial v},\quad \dot{v} = -\frac{\partial H}{\partial x}$$

  </div>

---

## Integrate Hamilton's equations
<div style="font-size:0.8em;line-height:1.25;">

<div style="color: var(--r-link-color);font-weight:2000;">$$\dot{x}=\frac{\partial H}{\partial v},\quad \dot{v} = -\frac{\partial H}{\partial x}$$</div>

<!-- Starting from $x_0$, we integrate along a _trajectory_ from $(x_{0}, v_{0})$ to $(x', v') -->
<!-- We can construct a **trajectory** of length $N_{\mathrm{LF}}$ leapfrog steps by: -->
1. Starting from $x_{0}$, resample the momentum $v_{0}\sim\mathcal{N}(0,
   \mathbb{1})$ and construct initial state: $\xi_{0} \equiv (x_{0}, v_{0})$
2. For $i = 1, 2,\ldots, N_{\mathrm{LF}}$:
   - $\xi_{i}=(x_{i}, v_{i})\rightarrow\xi_{i+1}\rightarrow\cdots\rightarrow \xi_{N_{\mathrm{LF}}-1} \rightarrow \xi_{N_{\mathrm{LF}}} = \xi'$
3. At the end of the trajectory, accept or reject the _proposal configuration_ $\xi'=\xi_{N_{\mathrm{LF}}} = (x', v')$ with probability $A(\xi'|\xi)$

<div id="left" style="font-size:0.8em;margin-top: 20px;">

<div id="note" style="margin:auto; margin-left:70px;margin-right: -60px;text-align:left; float: left; padding:10px; line-height:1.5;">

### <u>Leapfrog Step:</u>
<ol style="text-align: left; padding-right: 20px;margin-top:-10px;">
<li> $\,\, \tilde{v}\leftarrow v - \frac{\varepsilon}{2}\cdot \partial_{x} S(x)$</li>
<div id="bright"><li> $\,\, x'\leftarrow x + \varepsilon \tilde{v}$</li> </div>
<li> $\,\, v' \leftarrow \tilde{v} - \frac{\varepsilon}{2}\cdot \partial_{x} S(x')$</li>

</div>

</div>

<div id="right" style="font-size:0.8em;margin-top: 20px;">

<div id="note" style="margin:auto; padding:5px; text-align:left; float: right; border:none;line-height:1.5;">

### <u>Accept / Reject:</u>
<div style="padding-left:10px; margin-top:-10px;">

<div id="bright">
$x_{i+1}\leftarrow\begin{cases}
x'\text{ w/ prob. } A(\xi'|\xi),\\
x_{i} \text{ w/ prob. }1 - A(\xi'|\xi)
\end{cases}$
</div>
and  $A(\xi'|\xi)=\min\left\{1,\frac{p(\xi')}{p(\xi)}\left|\frac{\partial\xi'}{\partial\xi}\right|\right\}$

</div>
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

<!-- .slide: data-background="assets/l2hmc/hmc1.svg" -->

---

<!-- .slide: data-background="assets/l2hmc/generalized_leapfrog.svg" -->

---

<!-- .slide: data-background="assets/l2hmc/generalized_leapfrog_train.svg" -->

---

<!-- .slide: data-background="assets/l2hmc/lattice.svg" -->

---

## Hamiltonian Monte Carlo (HMC)

![](assets/hmc-crop.svg)

---

### Networks

<div style="max-width=99%">

![](assets/net_fns.svg)

</div>

---
	
<div class="sl-block" style="width: auto; height: auto;">
	<div class="sl-block-content notranslate" style="z-index: 15; background-color: rgba(109, 158, 235, 0.35); border-style: solid; border-radius: 5px; border-color: rgb(255, 255, 255); padding: 4px;">
		$p(\xi) = p(x)\cdot p(v)\cdot p(d)$
	</div>
</div>

<style>
@import url('https://fonts.googleapis.com/css2?family=Open+Sans&family=Roboto:wght@500&family=Source+Sans+Pro&display=swap');

:root {
    --r-main-font: 'Source Sans Pro', sans-serif;
    --r-heading-font: 'Open Sans', 'Roboto', Arial, Helvetica, sans-serif;
    --r-heading-text-transform: none;
    --r-heading1-size: 2.0em;
    --r-heading2-size: 1.5em;
    --r-heading3-size: 1.2em;
    --r-heading4-size: 1.15em;
    --r-link-color: #00A2FF;
    --r-link-color-dark: #f92672;
    --r-link-color-hover: #63ff51;
    --r-controls-color: #D7E6F3;
    --r-progress-color: #f20052;
    --r-selection-background-color: rgba(30, 60, 107, 0.9);
    --r-selection-color: #fff;
    --r-main-font-size: 40px;
    --r-main-color: #222;
    --r-block-margin: 20px;
    --r-heading-margin: 0 0 20px 0;
    --r-heading-color: #00A2FF;
    --r-heading-line-height: 1.2;
    --r-heading-letter-spacing: -0.05em;
    --r-heading-word-spacing: 0.5px;
}

.reveal {
    font-family: 'Source Sans Pro', sans-serif;
    font-size: var(--r-main-font-size);
    font-weight: normal;
    color: var(--r-main-color);
}

.reveal h1,
.reveal h2,
.reveal h3,
.reveal h4 {
    font-family: 'Open Sans', 'Roboto', Arial, Helvetica, sans-serif;
    margin: var(--r-heading-margin);
    color: var(--r-heading-color);
    font-family: var(--r-heading-font);
    font-weight: 800;
    line-height: var(--r-heading-line-height);
    letter-spacing: var(--r-heading-letter-spacing);
    word-spacing: var(--r-heading-word-spacing);
    text-transform: var(--r-heading-text-transform);
    text-shadow: var(--r-heading-text-shadow);
    word-wrap: break-word;
}

.reveal h1 {
    font-size: var(--r-heading1-size);
    font-weight: 1250;
}

.reveal h2 {
    font-size: var(--r-heading2-size);
}

.reveal h3 {
    font-size: var(--r-heading3-size);
    color: #1c1c1c;
}

.reveal h4 {
    font-size: var(--r-heading4-size);
    color: #61D836;
}

#left {
  margin: 0 0 15 10;
  text-align: left;
  float: left;
  z-index: -10;
  width: 48%;
  font-size: 0.85em;
}

#right {
  margin: 0 0 15 0;
  float: right;
  max-width: 48%;
  text-align: left;
  z-index: -10;
  width: 48%;
  font-size: 0.85em;
}
#dark_back {
    background-color: #1c1c1c;
    color: #efefef;
    .reveal a {
        color: #F92672;
        transition: color 0.15s ease;
    }
    .reveal a:hover {
        color: var(--r-link-color-hover);
    }
}
#bright {
    color: #FF4050;
}

#note {
    background-color: rgba(215, 230, 243, 0.5);
    border-radius: 5px;
    border-color: rgba(215, 230, 243, 0.5);
    padding: auto;
    margin: auto;
}

#halfsize {
    font-size: 0.5em;
}

</style>
