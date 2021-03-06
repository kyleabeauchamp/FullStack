---
title: erooM's Law for R&D Efficiency
subtitle: Producing a drug costs $2B and 15 years, with 95% fail rate

<center>
<img height=400 src=figures/eroom.png />
</center>


<footer class="source"> 
Scannell, 2012
</footer>


---
title: How can computers help design drugs?

<center>
<img height=475 src=figures/cruise.png />
</center>

<footer class="source"> 
Figure credit: @jchodera, Tom Cruise
</footer>



---
title: Counting Transitions

<center>

<img height=300 src=figures/NewPaths-2State.png />

$\downarrow$

$A = (111222222)$

---
title: Counting Transitions

<center>

$A = (111222222)$

$\downarrow$

$C = \begin{pmatrix} C_{1\rightarrow 1} & C_{1\rightarrow 2} \\\ C_{2 \rightarrow 1} & C_{2 \rightarrow 2} \end{pmatrix} =  \begin{pmatrix}2 & 1 \\\ 0 & 5\end{pmatrix}$

</center>

---
title: Estimating Rates

<center>
$C = \begin{pmatrix} C_{1\rightarrow 1} & C_{1\rightarrow 2} \\\ C_{2 \rightarrow 1} & C_{2 \rightarrow 2} \end{pmatrix} = \begin{pmatrix}2 & 1 \\\ 0 & 5\end{pmatrix}$

$\downarrow$

$T = \begin{pmatrix} T_{1\rightarrow 1} & T_{1\rightarrow 2} \\\ T_{2 \rightarrow 1} & T_{2 \rightarrow 2} \end{pmatrix} = \begin{pmatrix}\frac{2}{3} & \frac{1}{3} \\\ 0 & 1\end{pmatrix}$

</center>



---
title: Re-engineering sklearn for timeseries
subtitle: Sklearn uses 2D arrays; MSMB uses lists of arrays!


<pre class="prettyprint" data-lang="python">

# sklearn style
data = np.zeros((n_samples, n_features))

# MSMB style
data = [np.zeros((n_samples, n_features)) for i in range(num_trajectories)]

model.fit(data)

</pre>


---
title: MDTraj

<center>
<img height=250 src=figures/mdtraj_logo-small.png />
</center>


<footer class="source"> 
mdtraj.org <br>
McGibbon et al, 2014
</footer>



---
title: Trajectory munging with MDTraj
subtitle: Lightweight Pythonic API

<pre class="prettyprint" data-lang="python">
import mdtraj as md

trajectory = md.load("./trajectory.h5")
indices, phi = md.compute_phi(trajectory)
</pre>


<center>
<img height=300 src=figures/phi.png />
</center>

<footer class="source"> 
mdtraj.org <t>
McGibbon et al, 2014 <t>
</footer>


---
title: WANTED: Reliable, machine-readable, open archive of physicochemical measurements!
class: segue dark nobackground



---
title: Can we leverage ThermoML for forcefield validation?
class: segue dark nobackground




---
title: NIST Thermodynamics Research Center

<center>
<img height=500 src=figures/boulder3.jpg />
</center>


---
title: (Future) Application: Ligand Binding

<center>
<img height=450 src=figures/gprc_ligands.jpg />
</center>

<footer class="source"> 
Kobilka, 2012.  See also Shukla, 2013.
</footer>


---
title: Inverse dielectric constant is proportional to interaction strength

$$U(r) = \frac{1}{4 \pi \epsilon} \frac{q_1 q_2}{r} \propto \frac{1}{\epsilon}$$


---
title: Benchmarking neat liquid densities and dielectric constants

- Munge ThermoML with pyxb and pandas
- OpenMM 6.2
- GAFF / AM1-BCC (Antechamber + OpenEye)
- Converge each density to 0.0002 g / mL ($\approx$ expt. error)


<center>
<img height=200 src=figures/openmm.png />
</center>

<footer class="source"> 
PME + Langevin 1 fs + Monte Carlo Barostat + Fixed HBond Constraints + 1000 molecules per box
</footer>



---
title: Fixed charges fail to capture polarizability
subtitle: Observed: $\epsilon \approx 2.0$, Predicted: $\epsilon \approx 1.0$, $\Delta \Delta G_{solv} \approx$ 2 kcal / mol

<center>
<img height=400 src=figures/nonpolar_molecules.png />
</center>


---
title: Python packaging blues

<pre>

<font color="red">User: I couldn't really install the mdtraj module on my computer [...]
User: I tried easy_install and other things and that didn't work for me.</font>

</pre>

<center>
<img height=370 src=figures/dependencies0.png />
</center>
