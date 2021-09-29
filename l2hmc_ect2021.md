---
title: "l2hmc-qcd @ ECT 2021"
theme: simple
highlightTheme: github
---

<!-- .slide: data-background="#1c1c1c" -->
<!-- <h2 class="r-fit-text">Training Topological Samplers</h2> -->
## <h2 class="r-fit-text">Training Topological Samplers</h2>
### for Lattice Gauge Theories
### <div id="blue">[**Sam Foreman**](mailto://foremans@anl.gov)</div>
##### 09/29/2021

[<img align="left" width=10% src="assets/github.svg">](https://github.com/saforem2/l2hmc-qcd)
[<img align="right" width=30% src="assets/Argonne_cmyk_white.svg">](https://alcf.anl.gov)

---

# Generating Gauge Configurations

- In order to calculate physical quantities of interest, we 

---

## HMC: Leapfrog Integrator

- The ability to efficiently sample from complicated sampling from complicated distributions is a widely studied 
<img align="center" width=100% src="assets/single_chain.svg">

---

## <div style="color: #3B4CC0;">Critical Slowing Down</div>

<div id="left" style="font-size: 0.85em;">

- As $\beta\rightarrow\infty$, configurations tend to get stuck at $Q=\text{const}$.

- Equivalently, the _**tunneling rate**_: $\delta Q\equiv\left|Q_{i+1} - Q_{i}\right|\rightarrow 0$ as $\beta\rightarrow\infty$

- <div id="note" style="padding-left: 10px; padding-top: 5px; padding-bottom: 6px;">
  	<b><u>Goal</u></b>: Draw <i>independent</i> samples from target distribution $p(x)\propto e^{-\beta\,S(x)}$
  </div>

</div>

<div id="right">

<img align="right" width=90% src="assets/charge_freezing-crop.svg">

</div>

---

## Hamiltonian Monte Carlo (HMC)

<span style="text-align:left;">

- Introduce **momentum** <span id="blue">$v \sim \mathcal{N}(0,
  \mathbb{1})$</span> distributed independently of $x$

- The joint target density is then:
  $ p(x, v) = p(x) \cdot p(v) = e^{-\beta S(x)}\cdot e^{-v^{T}v / 2} =
  e^{-H(x, v)}$

- We can evolve the joint $\xi = (x, v)$ system using **Hamiltons equations**
  <div id="note" style="max-width:50%;padding:2px;text-align:center;">
  <div id="brightred">

  $ \dot{x} = \frac{\partial H}{\partial v},\quad \dot{v} = -\frac{\partial
  H}{\partial x}$

  </div>
  </div>

  along $H=\text{const.}$ from $(x_{0}, v_{0})\rightarrow (x^{\ast}, v^{\ast})$

</span>

---

## Leapfrog Integrator

<div style="text-align:left;">

<div style="font-size:0.65em">

- <div id="note" style="padding:5px;">
Goal: Integrate Hamilton's eqs:
$(\dot x, \dot v) =
\left(\frac{\partial H}{\partial{v}}, \frac{\partial
H}{\partial{x}}\right)$

</div>
</div>

<div style="font-size:0.65em;line-height:1.2;">

1. Starting from $x_{0}$, resample the momentum $v_{0}\sim\mathcal{N}(0,
\mathbb{1})$ and construct initial state: $\xi_{0} \equiv (x_{0}, v_{0})$

2. Integrate along a trajectory of $N_{\mathrm{LF}}$ leapfrog steps. For $i =
1, 2,\ldots, N_{\mathrm{LF}}-1$:
<div id="note" style="font-size:0.9em;padding:5px;text-align:center;width:auto;">
$($<span id="brightpink">$x_{i}$</span>$, v_{i})=\xi_{i}\rightarrow\xi':=\xi_{i+1}\rightarrow\cdots\rightarrow
\xi_{N_{\mathrm{LF}}-1} \rightarrow \xi_{N_{\mathrm{LF}}} = \xi^{\ast}= \,($<span id="blue">$x^{\ast}$</span>$,
v^{\ast})$
  </div>

3. At the end of the trajectory, accept or reject the <i>proposal configuration</i>
$\xi^{\ast}=\xi_{N_{\mathrm{LF}}} = (x^{\ast}, v^{\ast})$ with probability $A(\xi^{\ast}|\xi)$

</div>

<div style="align: center;">
<div id="left" style="font-size:0.7em;">
<!-- <div style="font-size:0.7em; max-width:40%"> -->

<div id="note" style="float:right; text-align:left; padding:10px; line-height:1.5;">

#### <u><b>Leapfrog Step:</b></u> 
<ol style="text-align: left;margin-top:-10px;">
<li> $\,\,\,\tilde v\,\leftarrow v - \frac{\varepsilon}{2}\partial_{x} S(x)$</li>
<li> $\,\, x'\longleftarrow x + \varepsilon\, \tilde v$</li>
<li> $\,\,v' \leftarrow \tilde v - \frac{\varepsilon}{2}\partial_{x} S(x')$</li>

</div>

</div>

<div id="right" style="font-size:0.7em;">

<div id="note" style="text-align:left; padding:10px; float: right; border:none;line-height:1.2;">

#### <u><b>Accept / Reject:</b></u>
<div style="padding-left:10px; margin-top:-10px;">

<span id="blue" style="color:var(--r-main-color);">
$x_{\mathrm{new}}\leftarrow\begin{cases}
\color{#228BE6}{x^{\ast}\text{ w/ prob. } A(\xi^{\ast}|\xi)},\\
\color{#F92672}{x_{i}} \text{ w/ prob. }1 - A(\xi^{\ast}|\xi),
\end{cases}$
</span>
$A(\xi^{\ast}|\xi)=\min\left\{1,\frac{p(\xi^{\ast})}{p(\xi)} \left|\frac{\partial\xi^{\ast}}{\partial\xi}\right|\right\}$
</div>

</div>
</div>

</div>
</div>

---

## HMC: Leapfrog Integrator

<img align="center" width=100% src="assets/hmc-crop.svg">

---

<section data-background-iframe="https://chi-feng.github.io/mcmc-demo/app.html"
          data-background-interactive>

---

## Hamiltonian Monte Carlo (HMC)

<div style="font-size: 0.75em;">

- Introduce auxiliary momentum $v \sim \mathcal{N}(0, \mathbb{1})$ distributed
  independently of $x$.

- Target distribution becomes:
$$ p(x, v) = p(x)\cdot p(v) = e^{-\beta S(x)}\cdot e^{-v^{T}v/2} $$

<img align="center" width=75% src="assets/hmc-crop.svg">

</div>

---

## Generalizing HMC

- **Require:**
   - reversibility: $p(a\rightarrow b) = p(b\rightarrow a)$
   - ergodicity 


---

## Generalizing HMC

- <span id="note" style="padding-left: 10px; max-width:70%; text-align: left; padding-top: 5px; padding-bottom: 6px;">
  	<b><u>Goal</u></b>: Generate proposal configuration 
  </span>

- Introduce persistent direction $d\sim\mathcal{U}(+, -)$ 
  - _forward_ (+) / _backward_ (-).
- **Target Distribution**: $p(\xi)=p(x)\cdot p(v)\cdot p(d)$
- **k^{th} Leapfrog Layer**:

---

![](assets/l2hmc/svgs/l2hmc4.svg)

---

<!-- .slide: data-background="assets/l2hmc/svgs/l2hmc5.svg" -->

---

<!-- .slide: data-background="assets/l2hmc/svgs/l2hmc6.svg" -->

---

<!-- .slide: data-background="assets/l2hmc/svgs/l2hmc7.svg" -->

---
	
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
    --r-heading-color: #222;
    --r-heading-line-height: 1.2;
    --r-heading-letter-spacing: -0.05em;
    --r-heading-word-spacing: 0.5px;
}

.reveal {
    font-family: var(--r-main-font), sans-serif;
    font-size: var(--r-main-font-size);
    font-weight: normal;
    color: var(--r-main-color);
}

.reveal h1,
.reveal h2,
.reveal h3,
.reveal h4 {
    font-family: var(--r-heading-font), 'Roboto', Arial, Helvetica, sans-serif;
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
    color: #333333;
}

#left {
  margin: 0 0 5px 5px;
  text-align: left;
  float: left;
  z-index: -10;
  width: 48%;
  font-size: 0.85em;
}

#right {
  margin: 0 0 5px 0;
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
#blue {
    color: #228BE6;
}
#bright {
    color: #00A2FF;
}
#green {
    color: #009051;
}
#pink {
    color: #E64980;
}
#brightpink {
    color: #F92672;
}

#brightred {
    color: #FA5252;
}

#noteinverse {
    background-color: #1c1c1c;
    border-radius: 5px;
    border-color: #1c1c1c;
    color: #efefef;
    padding: auto;
    margin: auto;
}

#note {
    background-color: rgba(240, 240, 240, 0.90);
    border-radius: 5px;
    border-color: rgba(240, 240, 240, 1.0);
    padding: auto;
    margin: auto;
}

#halfsize {
    font-size: 0.5em;
}

</style>
