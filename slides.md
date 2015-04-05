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
openmm.org
<span style="display:inline-block; width: 15px;"></span>
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
<span style="display:inline-block; width: 15px;"></span>
FAH OpenMM core developed by Yutong Zhao
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
title: Application: drugs and resistance

<img height="175" class="center" src=figures/mutation_diagram_SRC.png />

<img height="300" class="center" src=figures/res_muts-2.png />


<footer class="source"> 
cbioportal.org
</footer>

---
title: Long-lived states in methyltransferases
subtitle: Will guide docking, FEP efforts

<img width=480 src=figures/dihedral_map.png />  <img width=480 src=figures/dihedral_map_byrun.png />

---
title: MSMBuilder
subtitle: Finding meaning in massive simulation datasets


<center>
<img height=300 src=figures/msmbuilder.png />
</center>


<footer class="source"> 
msmbuilder.org
<span style="display:inline-block; width: 15px;"></span>
Bowman et al, 2009.
<span style="display:inline-block; width: 15px;"></span>
Beauchamp et al, 2012. 
<span style="display:inline-block; width: 15px;"></span>
McGibbon et al, 2014.
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
from msmbuilder import example_datasets, cluster, msm, dataset
from sklearn.pipeline import make_pipeline

path = "/home/kyleb/msmbuilder_data/alanine_dipeptide/"
trajectories = dataset.dataset(path + "*.dcd", topology=path + "ala2.pdb")

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
title: Takeaways

- Distributed GPU computing enables millisecond simulation
- MSMBuilder / MDTraj builds machine learning models of conformational dynamics
- omnia.md: Conda packaging of OpenMM, MSMBuilder, MDTraj, and more
- Ongoing work: characterize metastable states of kinases and methyltransferases to drive docking / FEP efforts

---
title: People

- John Chodera + ChoderaLab (MSKCC)
- Robert McGibbon (Stanford)
- Peter Eastman, Yutong Zhao (Stanford)
- Vijay Pande + PandeLab (Stanford)
- Jason Swails (Rutgers)
- Justin MacCallum (U. Calgary)
- Minkui Luo (MSKCC)

<footer class="source"> 
Jan-Hendrik Prinz, Patrick Grinaway, Danny Parton, Bas Rustenburg, Sonya Hanson
Greg Bowman, Christian Schwantes, TJ Lane, Vince Voelz, Imran Haque, Matthew Harrigan, Carlos Hernandez, Bharath Ramsundar, Lee-Ping Wang
Frank Noe, Martin Scherer, Xuhui Huang, Sergio Bacallado, Mark Friedrichs, Joy Ku
</footer>
