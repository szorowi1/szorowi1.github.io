---
title: "fMRI Detection Power"
layout: post
date: 2019-01-06 00:00
tag:
  - fmri
headerImage: false
description: "Estimating detection power of fMRI block designs."
category: blog
author: szoro
externalLink: false
---

Since starting the PhD, the one consistent piece of advice I keep hearing is
*nurture a writing habit.* Scientific writing is challenging (at least for me), and
the only way to improve is to just do it. So because I'm not producing a new paper
every week, this blog will be where I work to get better at communicating the sorts
of ideas of I'm thinking about en route to the PhD.

---

This spring, I am the assistant instructor for the cognitive neuroscience methods
course taken by the first year graduate students in the department. The course
covers a smattering of topics, but a majority of the curriculum is devoted to
covering fMRI methods. As such, in the run-up to the semester, I've been
brushing up on the logic of fMRI experimental design. In this post, I want to
review the theory underlying the conventional wisdom that the optimal block length
of an on-off block design experiment is 16-20s.

Let's start with the basics: Say we want to know which population(s) of neurons
are involved in perceiving faces. To find out, we design a paradigm consisting
of FACE trials (pictures of faces), SCRAMBLED trials (pictures of scrambled faces,
matched for luminance, color, etc.), and NULL trials (blank screen). These images
are presented to (consenting) participants while they are in the scanner. We assume
putative face-processing neurons will be more active (fire more) during FACE
trials. That is, we are interested in locating the area of the brain that has the
greatest BOLD signal change in response to FACE trials.

The question is: how long should we make each trial in order to bolster our ability
to detect this signal change? Several possibilities are presented below. We could
present blocks of face images for 4s (top), 16s (middle), or 64s (bottom). Given
just these traces, how can we assess their **detection power** (i.e. ability to
detect nonzero signal changes)?

<figure>
    <img class="bigger-image"
      src="../assets/images/post-001/designs.png" alt="designs"
      style="margin-top: 0px; margin-bottom: 20px"
    />
    <figcaption style="text-align: left">
      Estimated hemodynamic response to blocks of FACE (red) and SCRAMBLED (blue)
      trials. Grey boxes correspond to presentation time of FACE stimuli.
    </figcaption>
</figure>

Fortunately <a href="https://www.ncbi.nlm.nih.gov/pubmed/14741676">Liu &
Frank (2004)</a> have an answer for us, though it requires a little math. The
overall detection power of an experiment is defined as:

$$ R_{tot} = \frac{1}{ \frac{1}{N} \sum \frac{1}{R_{ij}} } $$

where the detection power for each individual contrast, \\( R_{ij} \\), is defined as:

$$ R_{ij} = \frac{ \left[ D_{ij}(X^T X)^{-1}D_{ij} \right]^{-1} }{h^Th} $$

Now, this may look daunting but it is actually fairly simple:

- \\( X \\) is the design matrix, i.e. the matrix of regressors from above.
- \\( h \\) is the assumed hemodynamic response function.
- \\( D \\) is a contrast vector, corresponding to the main effects, \\( [1,0], [0,1], \\)
  and pairwise contrasts, \\( [1,-1] \\).

Of these values, the most important is \\( X^TX \\), or the
<a href="https://en.wikipedia.org/wiki/Fisher_information">*Fisher
information matrix*</a>, which has some important properties that I will not go
into here. For the present purposes, the important thing to know to know is that
an optimal design will have large values along the diagonal of this matrix, and
small values in its off-diagonals. Why is this so?

Consider again the design matrix, \\( X \\). It is an \\( [N,K] \\) matrix where
\\( N \\) is the number of time points and \\( K \\) is the number of conditions.
Therefore, \\( X^TX \\) simply returns a \\( [K,K] \\) matrix. The diagonals of
this matrix are the dot product of the regressors with themselves. As such, it
is apparent when the diagonals of the Fisher information matrix are large: when
the regressors themselves, on average, deviate from zero. When the estimated BOLD
response differs greatly from zero, we should expect that nonzero changes in the
observed BOLD signal will be easier to detect. Hence, a design matrix with larger
diagonal elements in its corresponding Fisher information matrix is more optimal.

The converse is true for the off-diagonals. The off-diagonals of the Fisher information
matrix are computed from the pairwise dot product of the columns of the design
matrix. As such, the off-diagonals are large when, on average, the estimated BOLD
signal for two conditions deviate from zero *in the same direction at the same
time*. If we want to be able to resolve differences between conditions,
e.g. measure FACE devoid of interference from SCRAMBLED, then we want to measure
then we want the estimated BOLD signal of two conditions to be orthogonal. Hence,
a design matrix with smaller off-diagonal elements in its corresponding Fisher
information matrix is more optimal.

<div class="side-by-side">
    <div class="toleft">
        <p>The detection power of five experimental designs are presented on the
        right. A design with 4s block lengths has dramatically lower detection power.
        This is unsurprising in light of the discussion above. At 4s, the estimated
        BOLD signal for both conditions is both smaller in amplitude (smaller
        diagonals) and greatly overlapping (larger off-diagonals).</p>
    </div>

    <div class="toright">
        <img class="image" src="../assets/images/post-001/power1.png" alt="power1">
    </div>
</div>

Also in line with our expectations, designs with long block lengths have high
detection power. At longer block lengths, the estimated BOLD signal for both
conditions is larger in amplitude (large diagonals) and non-overlapping (small
off-diagonals). Note that the detection power is not much different for 16s, 32s,
and 64s. Then why is it said that the optimal block length is 16-20s?

The answer has to do with artifactual noise commonly observed with fMRI data.
Unfortunately, fMRI data is often contaminated by low frequency drifts. By the
same logic as above, a design is robust when it is nearly orthogonal to the nuisance
terms. If the on-off cycle of stimuli blocks is too slow, then it may overlap
with the low-frequency drifts (i.e. off-diagonal terms) thereby reducing overall
detection power. We can simulate this effect by including
<a href="https://en.wikipedia.org/wiki/Legendre_polynomials">Legendre polynomials</a>
(up to order 2) as nuisance regressors in the design matrix.

<div class="side-by-side">
    <div class="toleft">
        <p>The detection power of five experimental designs with (dotted) and without
        (solid) including Legendre polynomial regressors are presented on the right.
        With the inclusion of the low frequency drift terms, we observe an inverse-U
        in the detection power of the designs, peaking at the design with block
        lengths of 16s.</p>
    </div>

    <div class="toright">
        <img class="image" src="../assets/images/post-001/power2.png" alt="power2">
    </div>
</div>

And there you have it! The conventional wisdom of block designs explained through
a few lines of math and some basic intuitions about matrix algebra over estimated
BOLD signals.
