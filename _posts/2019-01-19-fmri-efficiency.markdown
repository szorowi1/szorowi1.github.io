---
title: "fmri estimation efficiency"
layout: post
date: 2019-01-19 00:00
tag:
  - fmri
headerImage: false
description: "Estimating efficiency of fMRI RER designs."
category: blog
author: szoro
externalLink: false
math: true
---

In a previous post, I reviewed how to calculate detection power for an fMRI
experiment design and why stimulus blocks of approximately 16s are optimal for
maximizing detection power. In this brief post, I will review a second optimality
metric of fMRI experiment designs: **estimation efficiency**. Whereas detection power
measures the ability to detect nonzero changes in the BOLD signal (under an
assumed HRF shape), estimation efficiency measures the ability to accurately model
the shape of the HRF itself.

To back up for a minute, why might we be interested in estimating the shape of
the HRF in the first place? Well, for one reason mismodeling the HRF can yield
systematic biases in measuring the amplitude of BOLD signal change thereby resulting
in spurious inferences about brain activity
<a href="https://www.ncbi.nlm.nih.gov/pubmed/19084070">(Lindquist et al. 2009)</a>.
For a second reason, the shape of the HRF is in itself meaningful as subcomponents
(e.g. time-to-peak, initial dip) ostensibly correspond to particular patterns of
neuronal activity. Thus, resolving the shape of the HRF--rather than simply assuming
it--can be insightful.

Now there are a number of methods by which to estimate the shape of the HRF. The
method I will demonstrate here, for which the estimation efficiency metric was
originally defined, is the finite impulse response (FIR) model. In an FIR model,
we generate a \\( [N, M \cdot K] \\) design matrix, where \\( N \\) is the number of
observed time points, \\( K \\) is the number of experimental conditions, and
\\( M \\) is the length of the FIR window, or the window of time of the hemodynamic
response that we want to resolve.

To provide a stronger intuition, let me give an example. Assume we collect fMRI
data during a (boring) experiment where we show the same stimulus to a participant
10 times. The stimulus is presented for 1s each time, with 20s between each presentation.
We collect a new image at a rate of 1 Hz (i.e. TR = 1). Finally, say we want to
estimate the shape of the HRF response out to 20 s. As such, the FIR design matrix
for this experiment is shape \\( N = 200 \\), \\( K = 1 \\), and \\( M = 20 \\).
Each column of the design matrix corresponds to one section of the FIR window;
in our example, the first column corresponds to 0-1s of the HRF, the second column
corresponds to 1-2s of the HRF, and so on. To indicate that a time point corresponds
to a given section of the FIR window, we simply put a 1 in the appropriate column
and leave 0s everywhere else. For example, we define \\( X[1,1] = 1 \\) to indicate
the first observation as the first 1s of the HRF in response to the first stimulus
presentation; we also define \\( X[21,2] = 1 \\) to indicate the 21st observation
as the second 1s of the HRF in response to the second stimulus presentation.

Before proceeding further, I will now introduce three example experiments that
will be useful for demonstrating the properties of estimation efficiency later.
These three experiments all share the same qualities: (1) they all involve
presenting stimuli from two conditions 60 times each; (2) each stimulus (120 total)
is presented for 1s; and (3) each experiment is 240s in total length, such
that each experiment is comprised of 120s of stimulus presentation time and
120s of null time.

These three experiments differ in the order of stimuli presentation. Experiment 1
will present stimuli in a blocked design. Experiment 2 will present stimuli of
alternating conditions every 2s (1s pause between each stimulus). experiment
3 will present stimuli in an (optimally) randomized design, with variable stimulus
condition order and variable jitter. The experiments and their corresponding
FIR design matrices are presented below. (We assume TR=1 and M=16.) As can easily
be observed, the FIR design matrices of the first two experiments have strong,
repeating structure whereas the third exhibits greater variability.

<figure>
    <img class="bigger-image"
      src="../assets/images/post-003/designs.png" alt="designs"
      style="margin-top: 0px; margin-bottom: 20px"
    />
    <figcaption style="text-align: left">
      Stimulus onsets (left column) and FIR design matrices (right column) for
      a block design (top), regular rapid event (middle), and random rapid event (bottom)
      designs.
    </figcaption>
</figure>

With the FIR design matrices in hand, we can now easily calculate the estimation
efficiency of each. Again following <a href="https://www.ncbi.nlm.nih.gov/pubmed/14741676">Liu &
Frank (2004)</a>, we define the overall estimation efficiency of an experiment as:

$$ C_{tot} = \frac{1}{ \frac{1}{N} \sum_{i \leq j} Tr \left[ C_{ij} \right] } $$

where the estimation efficiency for each individual contrast, \\( C_{ij} \\), is defined as:

$$ C_{ij} = L_{ij} \left( X^TX \right)^{-1} L_{ij}^T $$

where \\( L_{ij} \\) is a contrast matrix:

$$ L_{ij} = D_{ij} \otimes I_m $$

As from calculating detection power, \\( D \\) is a contrast vector, corresponding
to the main effects, \\( [1,0], [0,1], \\) and pairwise contrasts, \\( [1,-1] \\).
Thus, estimation efficiency has a formula similar to detection power, except now
collapsing across the entire FIR window of a particular condition. This means that
the estimation efficiency of an experiment bears a relationship to its
information matrix, \\( X^TX \\), similar to that observed for detection power.

<figure>
    <img class="bigger-image"
      src="../assets/images/post-003/information.png" alt="designs"
      style="margin-top: 0px; margin-bottom: 20px"
    />
    <figcaption style="text-align: left">
      Fisher information matrices for the three experiments. Matrices are of size
      [M,M].
    </figcaption>
</figure>

The Fisher information matrix for each experiment is presented above. The diagonal
elements reflect the number of observations for a given FIR window segment and
condition (60 in all of the above). The off-diagonal elements reflect the covariance
of two non-identical FIR window segments. As a result of the matrix inversion,
experiments will be more efficient to the extent that their diagonals are large
and their off-diagonals are small.

Intuitively, this makes sense. In order to efficiently estimate a given FIR window,
we would like it to be unconfounded by any other FIR window. If experiments have
regular structure, then given FIR windows will always occur at the same point
relative to one another, making it difficult to infer whether the BOLD signal at
a given point reflects the contribution of the HRF response from the *i*th or
*j*th window. Thus we can surmise that the block design and regular rapid event
designs will have low estimation efficiency.

<div class="side-by-side">
    <div class="toleft">
        <p>The efficiency of the three experiments are presented to the right.
        The first two experiments have very low efficiency as compared to the third.
        This highlights the importance of jittering for modeling the HRF fMRI experiments.
        The pseudo-random presentation of stimuli and null events reduces FIR window
        covariance resulting in more efficient estimation of the HRF.</p>
    </div>

    <div class="toright">
        <img class="image" src="../assets/images/post-003/efficiency.png" alt="efficiency">
    </div>
</div>
