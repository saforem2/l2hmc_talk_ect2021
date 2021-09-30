---
title: "l2hmc-qcd @ ECT 2021"
theme: simple
width: 960
height: 700
highlightTheme: github
output:
  revealjs::revealjs_presentation:
    self_contained: false
    reveal_plugins: ["chalkboard"]
    reveal_options:
      chalkboard:
        theme: whiteboard
        toggleNotesButton: false
---

<!-- .slide: data-background="#1c1c1c" -->
<!-- <h2 class="r-fit-text">Training Topological Samplers</h2> -->
## <h2 class="r-fit-text">Training Topological Samplers</h2>
## for Lattice Gauge Theories

### <span id="blue">[**Sam Foreman**](https://www.samforeman.me)</div>

<small>09/30/2021</small></span>

<small>Machine Learning for HEP, on and off the Lattice @ ECT* - Trento</small>

[`bit.ly/l2hmc-ect2021`](https://bit.ly/l2hmc-ect2021)


[<img align="left" width=10% src="assets/github.svg">](https://github.com/saforem2/l2hmc-qcd)
[<img align="right" width=30% src="assets/Argonne_cmyk_white.svg">](https://alcf.anl.gov)

---

# Motivation

- Want to calculate observables
  $$ \langle \mathcal{O}\rangle\propto\int\left[\mathcal{D}x\right]\mathcal{O}(x)e^{-S(x)} $$
- If we had _independent configurations_, we could approximate the integral as
  $$ \langle\mathcal{O}\rangle\simeq\frac{1}{N}\sum_{n=1}^{N}\mathcal{O}(x_{n})\Rightarrow \sigma^{2}=\frac{1}{N}\text{Var}\left[\mathcal{O}(x)\right] $$

---

## Motivation

- For independent samples: 
  <span style="font-size:0.6em;">
  $$\langle \mathcal{O}\rangle \propto\int\left[\mathcal{D}x\right]\mathcal{O}(x)e^{-S(x)}
  \simeq\frac{1}{N}\sum_{n=1}^{N}\mathcal{O}(x_{n})\Rightarrow
  \sigma^{2}=\frac{1}{N}\text{Var}\left[\mathcal{O}(x\right)]$$
  </span>
- Accounting for <span id="blue">autocorrelations</span>
  $$ \sigma^{2}=\frac{\color{#228BE6}{\tau_{\mathrm{int}}^{\mathcal{O}}}}{N}\text{Var}\left[\mathcal{O}(x)\right] $$
- <span id="blue">$\tau_{\mathrm{int}}^{\mathcal{O}}$</span> is known to scale <span
  id="red">exponentially</span> as we approach physical lattice spacing.


---

## Motivation

<!-- - The ability to efficiently sample from complicated sampling from complicated distributions is a widely studied  -->
- Generating independent configurations is currently a major bottleneck for
  lattice QCD.
- As the lattice spacing $a\rightarrow 0$ (or equivalently, $\beta \rightarrow \infty$),
  configurations get stuck in sectors of fixed gauge topology. 
  - Causes $\tau_{\mathrm{int}}$ to grow exponentially
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
  <div id="red">

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
   $($<span id="pink">$x_{i}$</span>$, v_{i})=\xi_{i}\rightarrow\xi':=\xi_{i+1}\rightarrow\cdots\rightarrow
   \xi_{N_{\mathrm{LF}}-1} \rightarrow \xi_{N_{\mathrm{LF}}} = \xi^{\ast}= \,($<span id="blue">$x^{\ast}$</span>$,
   v^{\ast})$
   </div>

3. At the end of the trajectory, accept or reject the <i>proposal configuration</i>
   $\xi^{\ast}=\xi_{N_{\mathrm{LF}}} = (x^{\ast}, v^{\ast})$ with probability $A(\xi^{\ast}|\xi)$

</div>

<div style="align: left;">
<div id="left" style="font-size:0.6em;">
<!-- <div style="font-size:0.7em; max-width:40%"> -->

<div id="note" style="float:left;margin-left:40px;text-align:left; padding:5px; line-height:1.5">

#### <u><b>Leapfrog Step:</b></u> 
  <ol style="text-align: left;margin-top:-10px;">
  <li> $\,\,\,\tilde v\,\leftarrow v - \frac{\varepsilon}{2}\partial_{x} S(x)$</li>
  <li> $\,\, x'\longleftarrow x + \varepsilon\, \tilde v$</li>
  <li> $\,\,v' \leftarrow \tilde v - \frac{\varepsilon}{2}\partial_{x} S(x')$</li>

</div>

</div>

<div id="right" style="font-size:0.6em;">

<div id="note" style="float:left; text-align:left; padding:2px; border:none;line-height:1.2;">

#### <u><b>Accept / Reject:</b></u>
  <div style="padding-left:10px; margin-top:-10px;">
  $x_{\mathrm{new}}\leftarrow\begin{cases}
  \color{#228BE6}{x^{\ast}}\text{ w/ prob. } A(\xi^{\ast}|\xi),\\
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

## Issues with HMC

<div style="text-align:left;font-size:0.8em;">

- Energy levels selected randomly $\rightarrow$ slow mixing!
- Cannot easily traverse low-density zones
- What do we want in a good sampler?
  - Fast mixing (small autocorrelations) 
  - Fast burn-in (quick convergence)
  - Ability to mix across energy levels and isolated modes

<img src="assets/hmc_traj_eps05.svg" width=49% align=center>
<img src="assets/hmc_traj_eps025.svg" width=49% align=center>

</div>

---

## Generalizing HMC

<div style="text-align:left;">

<!--
- <span id="note" style="padding-left: 10px; z-index:10; font-size:0.75em; max-width:90%; text-align: left; padding-top: 5px; padding-bottom: 6px;">
  	<b><u>Goal</u></b>: Generate independent proposal configurations to efficiently sample the topological charge $Q$
  </span>
-->

- **Main idea**:
  - Introduce six auxiliary functions <span id="red">$(s_x, t_x, q_x)$</span>
    (collectively, <span id="red">$\Lambda^{\pm}$</span>) and <span id="blue">$(s_v, t_v,
    q_v)$</span> (collectively, <span id="blue">$\Gamma^{\pm}$</span>) into the leapfrog updates
  - Each of these functions are parameterized by <span id="green">weights $\theta$</span> in a neural network
    - Based on `RealNVP` [arXiv:1605.08803](https://arxiv.org/abs/1605.08803)
- **Require**:
  - reversibility: $p(a\rightarrow b) = p(b\rightarrow a)$
  - detailed balance: $p(x') K(x|x') = p(x) K(x'|x)$
  - ergodicity

</div>

---

## Generalizing the Leapfrog Update

<div style="float:left; max-width:44%; font-size:0.9em; text-align: left;">

<span id="red"><b><u>L2HMC Update</u></b></span>: `\(\xi_{k}\rightarrow\xi''_{k}\)`
<small>Note that we split the $x$ update into two complementary sub-updates in order to preserve reversibility.</small>
<img src="assets/generalized_leapfrog.svg">
<!-- <span style="font-size:0.40em;">
<b>Fig. (a)</b> Update steps for performing a single L2HMC update (left).
</span> -->

</div>

<div style="float:right; max-width:55%;">

<span style="text-align:left;font-size:0.48em;">
<b>Fig. (a)</b> Diagram illustrating the L2HMC leapfrog update</b>
</span>
<img src="assets/network.svg" width=90%>
<small>Compare with the HMC update</small>

<div id="note" style="text-align:left; max-width:75%; padding:2px;
font-size:0.6em;background-color:rgba(127,203,233,0.2); padding:3px;">

#### <u><b>HMC Update:</b></u> 
<ol style="text-align: left;margin-top:-10px;">
<li> $\,\,\,v'\,\leftarrow v - \frac{\varepsilon}{2}\partial_{x} S(x)$</li>
<li> $\,\, x'\longleftarrow x + \varepsilon\, v'$</li>
<li> $\,\,v'' \leftarrow v' - \frac{\varepsilon}{2}\partial_{x} S(x')$</li>
</ol>

</div>
</div>

---

## Generalizing HMC

<img src="assets/network_iterate.svg" width=85%>

---

### Leapfrog Layer: Details

<div style="font-size:0.6em; text-align:left;">

- Introduce persistent direction $d\sim\mathcal{U}(+, -)$ (_forward_ / _backward_)

- **Target Distribution**: $p(\xi) = p(x)\cdot p(v)\cdot p(d)$

- $k^{th}$-Leapfrog Layer: `\(\xi_{k} = (x_{k}, v_{k}, \pm)\rightarrow (x''_{k}, v''_{k}, \pm)\)`
<div id="note" style="font-size:0.9em;padding:1.5px;text-align:center;max-width:90%;">
$(\color{#F92672}{x_{k}}, v_{k})=\xi_{k}\rightarrow\xi':=\xi_{k+1}\rightarrow\cdots\rightarrow
\xi_{N_{\mathrm{LF}}-1} \rightarrow \xi_{N_{\mathrm{LF}}} = \xi^{\ast}= \,(\color{#228BE6}{x^{\ast}},
v^{\ast})$
</div>

- Construct a trajectory by passing $\xi_{k}$ through $k\in\{1, 2, \ldots,
  N_{\mathrm{LF}}\}$ leapfrog layers.
![](assets/network_functions.svg)

</div>

---

<!-- UPDATE WITH NETWORK AND FUNCTIONS -->
<span style="font-size:0.5em;">
<b>Fig. (a)</b> Illustration of the generalized leapfrog update (top)
</span>
<!-- <div style="align:top margin-bottom:0px;"> -->

<div style="float:center; font-size:0.5em; text-align: center;">
<img src="assets/leapfrog_update_combined.svg">
</div>
<div style="float:center; font-size:0.5em; text-align: center;">
<b>Fig. (b)</b> Details of the functions $\Gamma^{\pm}$, $\Lambda^{\pm}$ for
updating $v$ and $x$ respectively (bottom);
</div>
<img src="assets/network_functions.svg">

---

<section id="gmm">

## Toy Example: GMM $\in \mathbb{R}^{2}$

<div style="text-align:left; font-size:0.8em;">

- <div id="note" style="padding:2px;"><b><u>Goal:</u></b> Train our sampler to
  effectively sample from both modes of the target distribution.</div>
- Maximize <span id="blue">_expected squared jump distance_</span>
  <div id="note" style="text-align:center;padding:0.9px;font-size:0.75em;">
  \[\mathcal{L}_{\theta}(\xi', \xi) \equiv
  \color{#228BE6}{\mathbb{E}_{p(\xi)}\left[A(\xi'|\xi)\cdot \delta(\xi',
  \xi)\right]}\]
  </div>
  where  $\delta(\xi, \xi') ={\lVert x - x'\rVert}^{2}$ is the squared jump
  distance between $x'$ and $x$.

<img src="assets/hmc_traj_eps05.svg" width=48% align=center>
<img src="assets/hmc_traj_eps025.svg" width=48% align=center>

</div>

---

## Toy Example: GMM $\in \mathbb{R}^{2}$

![](assets/iso_gmm_chains.svg)

---

**Training Algorithm**

<img src="assets/training_alg.svg" width=90% align="center">

---
## Annealing Schedule
<div style="font-size:0.8em;">

Introduce an _annealing schedule_ during the training phase:

<div style="font-size:0.8em;">

`\[\left\{\gamma_{t}\right\}=\left\{\gamma_{0}, \gamma_{1}, \ldots,
\gamma_{N-1}, \gamma_{N}\right\},\text{ with}\]`
`\[\gamma_{0}<\gamma_{1}<\gamma_{2}<\cdots<\gamma_{N}\equiv 1, \text{ and}\]`
`\[|\gamma_{t+1}-\gamma_{t}|\ll 1 \]`

</div>

- For $\lVert\gamma_{t}\rVert<1$, this helps to rescale (shrink) the energy
  barriers between isolated modes

- Allows our sampler to explore previously inaccessible regions of the target distribution

- Target distribution becomes <span id="blue">$p_{t}(x)\propto
  e^{-\gamma_{t}S(x)}$</span> for $t = 0, 1, \ldots, N$

</div>
---

### Lattice Gauge Theory

<div id="left" style="max-width:43%;font-size:0.7em;"> 

- **Link variables**: 
<span id="blue">$U_{\mu}(x) = e^{i x_{\mu}(n)}\in U(1)$</span>
with $x_{\mu}(n)\in[-\pi,\pi]$

- **Wilson Action**:
<span id="blue">$S_{\beta}(x) = \beta\sum_{P} 1 - \cos x_{P}$</span>
with $x_{P}= x_{\mu}(n) + x_{\nu}(n+\hat{\mu})-x_{\mu}(n+\hat{\nu})-x_{\nu}(n)$

- <b>Topological Charge</b>:
  - <span id="note" style="padding:5px;font-size:0.9em;background:#D0F3D5;margin-bottom:10px;">$Q_{\mathbb{R}} =
  \frac{1}{2\pi}\sum_{P} \sin x_{P}\in\mathbb{R}$ ✅</span>
  <span style="font-size:0.5em;">(continuous, differentiable)</span>

  - <span id="note" style="padding:5px;font-size:0.9em;background:#F7C2CC;">$Q_{\mathbb{Z}} =
  \frac{1}{2\pi}\sum_{P} \left\lfloor
  x_{P}\right\rfloor\in\mathbb{Z}$ ❌</span>
  <span style="font-size:0.5em;">(discrete, hard to work with)</span>

  here $\left\lfloor x_{P}\right\rfloor =
  x_{P}-2\pi\left\lfloor\frac{x_{P}+\pi}{2\pi}\right\rfloor$

</div>

<div id="right">

<img src="assets/plaq_tikz.svg" width=75% align="right">

</div>
---

# Loss Function

<div style="font-size:0.9em;">

- Maximize the <span id="blue">_expected squared charge difference_ </span>:
  <div id="note" style="padding:1px;">
  \[\begin{equation}
  \mathcal{L}(\theta) = \color{#228BE6}{\mathbb{E}_{p(\xi)}}
  \left[-\color{#FA5252}{{\delta Q}}^{2}_{\color{#FA5252}{\mathbb{R}}}(\xi', \xi)\cdot
  A(\xi'|\xi)\right]
  \end{equation}\]
  </div>
- Where $\color{#FA5252}{\delta Q_{\mathbb{R}}}$ is the <span id="red">tunneling rate</span>
  $$\delta Q_{\mathbb{R}}^{2}(\xi',\xi)=\left(Q_{\mathbb{R}}(x') - Q_{\mathbb{R}}(x)\right)^{2}$$
- And $A(\xi'|\xi)$ is probability of accepting the proposal configuration $\xi'$.
  $$ A(\xi'|\xi) = \min\left(1, \frac{p(\xi')}{p(\xi)}\left|\frac{\partial \xi'}{\partial \xi^{T}}\right|\right\)$$

</div>

---

<div style="text-align: center;font-size:0.5em;">

<img src="assets/autocorr_new.svg" width=45% align=center>
<b> Fig (a.)</b>  Comparison of $\tau_{\mathrm{int}}^{Q_{\mathbb{Z}}}$

<b> Fig (b.)</b> Topological charge history $Q_{\mathbb{Z}}$ vs MD time units.
for both the trained model (red) and HMC (black)

<img src="assets/charge_histories.svg" width=100%>

</div>

---

## $\tau_{\mathrm{int}}^{Q_{\mathbb{Z}}}$ at $\beta = 4$
<img src="assets/tint_beta4.svg" width=100%>

---

## Effective Action: $S_{\mathrm{eff}}(x)$
<img src="assets/plaqsf_beta.svg" width=82%>

---

# Interpretation
<img src="assets/ridgeplots.svg" width=100%>
<small>
<b>(a.)</b> Deviation in the average plaquette (left);
<b>(b.)</b> Real-valued topological charge (middle);
<b>(c.)</b> Effective energy (right);

<b>Fig.</b> Illustration of how different observables evolve over a
single L2HMC trajectory.
</small>

---

## Interpretation

<img src="assets/plaqsf_ridgeplot.svg" width=48% align=center>
<img src="assets/Hf_ridgeplot.svg" width=48%" align=center>
<small><b>Fig.</b> Illustration of how the trained model artificially
increases the energy towards the middle of the trajectory, allowing the sampler
to tunnel between isolated sectors.
<!-- <img src="assets/plaqsb_ridgeplot.svg" width=34% align=center>
<img src="assets/Hb_ridgeplot.svg" width=34%" align=center> -->

---

# Interpretation

<img src="assets/plaqsf_ridgeplot1.svg" width=48% align=center>
<img src="assets/Hwf_ridgeplot1.svg" width=46% align=center>

---

## [<img width=10% src="assets/github_black.png" align=center>](https://github.com/saforem2/l2hmc-qcd) [l2hmc-qcd](https://github.com/saforem2/l2hmc-qcd)

- Source code publicly available

- Both `pytorch` and `tensorflow` implementations with support for distributed training, automatic checkpointing, etc.

- Generic interface, easily extensible

- <b>Work in progress</b> scaling up to 2D, 4D $SU(3)$

---

## Non-Compact Projection 
<small>[arXiv:2002.02428](https://arxiv.org/abs/2002.02428)</small>

<div style="font-size:0.8em;">

- Project $x \in[-\pi, \pi]$ onto $\mathbb{R}$ using a transformation $z = g(x)$:
  $$ z = \tan\left(\frac{x}{2}\right) $$
- Perform the update in $\mathbb{R}$:
  $$ z' = m^{t}\odot z + \bar{m}^{t}\odot \left[\alpha z + \beta\right]$$
- Project back to $[-\pi, \pi]$ using $x = g^{-1}(z)$:
  $$ x = 2 \tan^{-1}(z) $$

</div>

---

# Non-Compact Projection

- Combine into a single update:
  $$ x' = \color{#228BE6}{m^{t}}\odot x +
  \color{#FA5252}{\bar{m}^{t}}\odot\left[2\tan^{-1}\left(\alpha\tan\left(\frac{x}{2}\right)\right)+\beta\right]
  $$
- With corresponding Jacobian:
  $$ \frac{\partial x'}{\partial x} = \frac{\exp(\varepsilon s_{x})}{\cos^{2}(x/2)+exp(2\varepsilon s_{x})\sin(x/2)} $$


</div>

---

## Acknowledgements

<div id="left">

### Collaborators:
 - Xiao-Yong Jin
 - James C. Osborn

### References:
 - [Link to slides](https://bit.ly/l2hmc-ect2021)
 - [Link to github](https://github.com/saforem2/l2hmc-qcd)
 - [reach out!](mailto://foremans@anl.gov)
 - [Link to HMC demo](https://chi-feng.github.io/mcmc-demo/app.html)
 - [arXiv:2105.03418](https://arxiv.org/abs/2002.02428)
 - [arXiv:2002.02428](https://arxiv.org/abs/2002.02428)


</div>

<div id="right">

### Huge thank you to:
 - Yannick Meurice
 - Norman Christ
 - Akio Tomiya
 - Luchang Jin
 - Chulwoo Jung
 - Peter Boyle
 - Taku Izubuchi
 - Critical Slowing Down group (ECP)
 - ALCF Staff + Datascience Group

</div>

<small> 
This research used resources of the Argonne Leadership Computing Facility,
which is a DOE Office of Science User Facility supported under Contract
DE-AC02-06CH11357.
</small>

---

### Network Architectures

<img src="assets/dynamics_xnet0.png"  align=center width=45%>

---

### Network Architectures

<img src="assets/dynamics_vnet.png"  align=center width=36%>

---
<style>
@import url('https://fonts.googleapis.com/css2?family=Open+Sans&family=Roboto:wght@500&family=Source+Sans+Pro&display=swap');

:root {
    --r-heading-text-transform: none;
    --r-heading1-size: 2.0em;
    --r-heading2-size: 1.5em;
    --r-heading3-size: 1.2em;
    --r-heading4-size: 1.15em;
    --r-link-color: #00A2FF;
    --r-link-color-dark: #f92672;
    --r-link-color-hover: #63ff51;
    --r-controls-color: #228BE6;
    --r-progress-color: #228BE6;
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
    font-weight: 1000;
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
#lighpink {
    color: #E64980;
}
#pink {
    color: #F92672;
}

#red {
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
