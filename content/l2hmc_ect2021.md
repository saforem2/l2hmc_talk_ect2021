---
title: Training Topological Samplers for Lattice Gauge Theory
theme: white
revealOptions:
transition: "fade"
---

---

<!-- .slide: data-background="#1c1c1c" -->
<h1 class="r-fit-text">Training Topological Samplers</h1>
<h2 class="r-fit-text">for Lattice Gauge Theories</h2>

# [**Sam Foreman**](mailto://foremans@anl.gov)

### 09/28

[<img align="left" width=10% src="attachments/github.svg">](https://github.com/saforem2/l2hmc-qcd)
[<img align="right" width=30% src="attachments/Argonne_cmyk_white.svg">](https://alcf.anl.gov)

---

<div id="left">

## Critical Slowing Down

- As $\beta\rightarrow\infty$, configurations tend to get stuck, $Q=\text{const}$.
- Or, equivalently, the **tunneling rate** $\delta Q\equiv\left|Q_{i+1} - Q_{i}\right|\rightarrow 0$ as $\beta\rightarrow\infty$
- <div class="w3-panel w3-leftbar w3-border-blue w3-xlarge" style="background-color:rgba(179,223,255,20);padding-left: 10px; padding-top: 5px; padding-bottom: 6px;">
  	<b>Goal</b>: Draw <i>independent</i> samples from target distribution $p(x)\propto e^{-\beta\,S(x)}$
  </div>
  <div id="ulc">
  </div>

</div>

<div id="right">

![Icon](attachments/charge_freezing-crop.svg)

</div>

---

<div id="left">
	<div class="w3-panel w3-leftbar w3-border-blue">
		<b>Goal</b>: Draw
	</div>

### Critical Slowing Down

- **Goal**: Draw _independent_ samples from some target distribution $p(x)$
  - Generating independent gauge configurations is a major bottleneck for LatticeQCD.
- **Topological Freezing**: Define $\delta Q\equiv Q_{i+1} - Q_{i}$
  - As we approach the continuum limit $\beta\rightarrow\infty$, $

</div>

<div id="right">
	<p align="right">
		<img src="attachments/charge_freezing/chargesHMC-crop.svg">
	</p>
</div>

---

<link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
