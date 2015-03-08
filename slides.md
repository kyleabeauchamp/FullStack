% title: Engineering a full python stack for biophysical computation
% author: Kyle A. Beauchamp
% author: Slides here: goo.gl/rKGhzZ
% favicon: figures/membrane.png


---
title: The atomistic basis of disease

...EVKMD<b><font color="black">A</font></b>EFRHDS...  ...EVKMD<b><font color="black">T</font></b>EFRHDS...  ...EVKMD<b><font color="black">V</font></b>EFRHDS...

<img width=300 src=figures/alanine.png />  <img width=300 src=figures/threonine.png />   <img width=300 src=figures/valine.png />

<footer class="source"> 
Jonsson, 2012  A673 T673 V673
</footer>



---
title: Molecular dynamics
subtitle:  Predict experiments quantitatively, rationalize function, design molecules

<center>
<img height=300 src=figures/multiscale-cartoon.png />
<video id="sampleMovie" src="movies/shaw-dasatanib-2.mov" loop=\"true\ autoPlay=\"true\  width="512" height="384"></video>
</center>


<footer class="source"> 
Movie Credit: Shan et al: J. Am. Chem. Soc. (2011).  Dasatinib / src
</footer>


---
title: Outline

- Simulating Biological Molecules: OpenMM + Folding@Home
- Inference on atomistic simulations: MSMBuilder + MDTraj
- A full stack for biophysical computation: Omnia.md
- Application: Benchmarking Small Forcefields


---
title: How do we reach biological timescales?
subtitle: The timescale gap: $10^{12}$

<center>
<img height=450 src=figures/protein_timescales.jpg />
</center>

<footer class="source">
Church, 2011.
</footer>


---
title: Molecular dynamics is (finally) fast
subtitle: A \$999 GPU buys 128 ns / day on solvated proteins

<center>
<img height=420 src=figures/TitanNew.jpg />
</center>

<footer class="source"> 
GTX Titan, DHFR, OpenMM 6.2 (openmm.org) See also: Gromacs, AMBER, Charmm, NAMD, DESMOND, ACEMD
</footer>

---
title: OpenMM
subtitle: GPU accelerated molecular dynamics

- Extensible C++ library with Python wrappers
- CUDA, OpenCL, CPU

<center>
<img height=300 src=figures/openmm.png />
</center>

<footer class="source"> 
openmm.org <br>
Eastman et al, 2012.
</footer>

---
title: OpenMM powers Folding@Home

- GPUs only take us to ~100 ns / day
- Largest distributed computing project
- 10,000 GPUs, milliseconds in aggregate!

<center>
<img height=300 src=figures/folding-icon.png />
</center>


<footer class="source"> 
http://folding.stanford.edu/
</footer>

---
title: Traditional simulation analysis: movies

<center>
<img height=400 src=figures/NewPaths1.png />
</center>
---
title: Massively parallel simulation?

<center>
<img height=400 src=figures/NewPaths2.png />
</center>


---
title: Introduction to Markov state models

<center>
<img height=400 src=figures/NewPaths3.png />
</center>

<footer class="source"> 
See work by Chodera, Bowman, Beauchamp, Pande, Noe, Huang, Voelz, Hummer, Prinz, Singhal
</footer>


---
title: Application: millisecond protein folding


<center>
<img height=450 src=figures/NTL9_network.jpg />
</center>

<footer class="source"> 
Voelz, Bowman, Beauchamp, Pande. J. Am. Chem. Soc., 2010
<span style="display:inline-block; width: 15px;"></span>
Beauchamp, et al, JCTC 2011
<span style="display:inline-block; width: 15px;"></span>
Beauchamp, et al, PNAS 2011
<span style="display:inline-block; width: 15px;"></span>
Beauchamp, et al, PNAS 2012
</footer>


---
title: Application: chemotherapy resistance

<img height="175" class="center" src=figures/mutation_diagram_SRC.png />

<img height="300" class="center" src=figures/res_muts-2.png />


<footer class="source"> 
cbioportal.org
</footer>

---
title: MSMBuilder
subtitle: Finding meaning in massive simulation datasets


<center>
<img height=300 src=figures/msmbuilder.png />
</center>


<footer class="source"> 
msmbuilder.org <br>
<span style="display:inline-block; width: 15px;"></span>
Bowman et al, 2009.
<span style="display:inline-block; width: 15px;"></span>
Beauchamp et al, 2012. 
</footer>


---
title: MSMBuilder3: design

<div style="float:right; margin-top:-100px">
<img src="figures/flow-chart.png" height="600">
</div>

Builds on [scikit-learn](http://scikit-learn.org/stable/) idioms:

- Everything is a `Model`.
- Models are `fit()` on data.
- Models learn `attributes_`.
- `Pipeline()` concatenate models.

<footer class="source"> 
http://rmcgibbo.org/posts/whats-new-in-msmbuilder3/
</footer>


---
title: MSMBuilder3: demo

<pre class="prettyprint" data-lang="python">
from msmbuilder import example_datasets, cluster, msm
from sklearn.pipeline import make_pipeline

dataset = example_datasets.alanine_dipeptide.fetch_alanine_dipeptide()  # From Figshare!
trajectories = dataset["trajectories"]  # List of MDTraj Trajectory Objects

clusterer = cluster.KCenters(n_clusters=10, metric="rmsd")
msm_model = msm.MarkovStateModel()

pipeline = make_pipeline(clusterer, msm_model)
pipeline.fit(trajectories)

</pre>

<footer class="source"> 
msmbuilder.org <br>
</footer>

---
title: MDTraj: trajectory IO and featurization

- Multitude of formats (PDB, DCD, XTC, HDF, CDF, mol2)
- Geometric trajectory analysis (distances, angles, RMSD)
- Numpy / SSE kernels enable Folding@Home scale analysis


<div style="float:left;">
<br>

<pre class="prettyprint" data-lang="python">

import mdtraj as md

trajectory = md.load("./trajectory.h5")

indices, phi = md.compute_phi(trajectory)


</pre>
</div>


<div style="float:right;">
<img src="figures/phi.png" height="300">
</div>



<footer class="source"> 
mdtraj.org
<span style="display:inline-block; width: 15px;"></span>
McGibbon et al, 2014.
<span style="display:inline-block; width: 15px;"></span>
Haque, 2014.
</footer>

---
title: A full stack for biophysical computation

<pre>
conda config --add channels http://conda.binstar.org/omnia
conda install omnia
</pre>

- OpenMM 6.2
- MDTraj 1.3
- MSMBuilder 3.0
- Yank 1.0 (beta)
- pymbar 2.1
- EMMA

<footer class="source"> 
omnia.md
</footer>


---
title: Benchmarking small molecule forcefields at the scale of NIST

- Small molecule forcefields need work

<center>
<img height=400 src=figures/mobley_dielectric.png />
</center>

<footer class="source"> 
Fennell, Mobley.  J. Phys. Chem. B. 2014
</footer>


---
title: Data access is killing forcefields

- Thousands of parameters = thousands of measurements
- Most datasets are heterogeneous, offline, and static

<center>
<img height=350 src=figures/stack-of-old-books.jpg />
</center>


---
title: Data capture at NIST/TRC: ThermoML


<center>
<img height=540 src="figures/pipeline.png"/>
</center>

---
title: ThermoML is rapidly growing

<center>
<img height=475 src=figures/thermoml_by_time_new.png />
</center>

<footer class="source"> 
Figure from Chiraco, J. Chem. Eng. Dat., 2013.
</footer>


---
title: Falsifying forcefields using neat liquid density and dielectric constants

- Sensitive to nonbonded parameters
- Simple ensemble average geometric interpretation

$$\rho = \langle \frac{M}{V} \rangle$$

$$\epsilon = 1 + \frac{4\pi}{3} \frac{\langle \mu \cdot \mu \rangle - \langle \mu \rangle \cdot \langle \mu \rangle}{V k_B T}$$

<footer class="source"> 
See also van der Spoel, JCTC, 2011 and Fennell, 2012.
</footer>


---
title:  Densities are in the ballpark

<center>
<img height=500 src=figures/densities_thermoml.png />
</center>

<footer class="source"> 
Beauchamp et al, In Preparation.
</footer>

---
title:  Static dielectric constants are consistently underestimated

<center>
<img height=450 src=figures/dielectrics_thermoml_nocorr_loglog.png />
</center>


<footer class="source"> 
Beauchamp et al, In Preparation.
</footer>

---
title: Fixed charges fail to capture polarizability
subtitle: Observed: $\epsilon \approx 2.0$, Predicted: $\epsilon \approx 1.0$, $\Delta \Delta G_{solv} \approx$ 2 kcal / mol

<center>
<img height=400 src=figures/nonpolar_molecules.png />
</center>


---
title: Atom counting predicts molecular polarizability to within 2%

<center>
<img height=100 src=figures/sales_title.png />
</center>

$$\alpha = 1.53 n_C + 0.17 n_H + 0.57 n_O + 1.05 n_N + 2.99 n_S + \\ 2.48 n_P + 0.22 n_F + 2.16 n_{Cl} + 0.32 $$

$$\epsilon_{corrected} = \epsilon_{MD} + 4 \pi N  \frac{\alpha}{\langle V \rangle}$$


<footer class="source"> 
Sales, 2002
</footer>
---
title: Empirical atomic polarizability corrections reduce bias

<center>
<img height=450 src=figures/dielectrics_thermoml_loglog.png />
</center>


<footer class="source"> 
Beauchamp et al, In Preparation.
</footer>

---
title: Where do we go from here?

- Scale up, real-time simulation, CI, web frontend
- Perform new experiments in automated wetlab
- Bayesian (MCMC) forcefield / experimental design


<center>
<img height=250 src=figures/robot_image.jpg />
</center>


---
title: Bayesian Energy Landscape Tilting

- Infer conformational ensembles from simulation <b>and</b> ambiguous experiments
- Characterize posterior through MCMC
- Error bars on equilibrium <b>and</b> structural features

<center>
<img height=250 src=figures/mcmc.png />    <img height=250 src=figures/bpti_raw.png />
</center>


<footer class="source"> 
Beauchamp, Pande, Das.  Biophys. J.  2014.
</footer>




---
title: Takeaways

- Distributed GPU computing enables millisecond simulation
- MSMBuilder / MDTraj builds machine learning models of conformational dynamics
- omnia.md: Conda packaging of OpenMM, MSMBuilder, MDTraj, and more
- ThermoML: towards automated benchmarking of atomistic models

---
title: People

- John Chodera + ChoderaLab (MSKCC)
- Julie Behr (MSKCC)
- Kenneth Kroenlein (NIST)
- Robert McGibbon (Stanford)
- Peter Eastman (Stanford)
- Vijay Pande + PandeLab (Stanford)
- Joy Ku (Stanford)
- Jason Swails (Rutgers)
- Justin MacCallum (U. Calgary)

<footer class="source"> 
Jan-Hendrik Prinz, Danny Parton, Bas Rustenburg, Sonya Hanson
Greg Bowman, Christian Schwantes, TJ Lane, Vince Voelz, Imran Haque, Matthew Harrigan, Carlos Hernandez, Bharath Ramsundar, Lee-Ping Wang
Frank Noe, Martin Scherer, Xuhui Huang, Sergio Bacallado, Mark Friedrichs
</footer>
