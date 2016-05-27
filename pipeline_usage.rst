DWI Pre-processing for Quantitative Analysis
============================================

Introduction
------------
This tutorial explains the required pre-processing steps for downstream applications that depend on FOD images for quantitative analysis (e.g. `Fixel-Based Analysis <http://userdocs.mrtrix.org/en/latest/workflows/fixel_based_analysis.html>`_ of Apparent Fibre Density, as well as `SIFT <http://userdocs.mrtrix.org/en/latest/workflows/sift.html>`_-based connectome analysis). 

Most DWI models derive quantitative measures by using the ratio of the DW signal to the b=0 signal within each voxel. This voxel-wise b=0 normalisation implicitly removes intensity variations due to T2-weighting and RF inhomogeneity. However, unless all compartments within white matter are modelled accurately (e.g. intra- and extra-axonal space, myelin, cerebral spinal fluid (CSF) and grey matter partial volumes), the proportion of one compartment in a voxel may influence another. For example, if CSF partial volume at the border of white matter and the ventricles is not taken into account, then a voxel-wise normalisation performed by dividing by the b=0 (which has a long T2 and appears brighter in CSF than white matter in the T2-weighted b=0 image), will artificially reduce the DW signal from the white matter intra-axonal (restricted) compartment, ultimately changing the derived quantiative measures. Multi-compartment diffusion MRI models aspire to model multiple compartments to mitigate these issues. However, in practice current models are limited by restrictions/assumptions placed on the different compartments, and all require multiple b-value acquisitions and therefore longer scan times. 

`Apparent Fibre Density (AFD) <http://www.ncbi.nlm.nih.gov/pubmed/22036682>`_ is a Fibre Orientation Distribution (FOD)-derived measure that was developed to enable fibre-specific quantitative analysis using single-shell HARDI data. AFD is based on the assumption that under certain conditions (high b-value of ~3000 s/mm2, typical clinically achievable diffusion pulse duration, typical axon diameters in the range 1-4µm), the DW signal arising from the extra-axonal space (including CSF) is fully attenuated, and the remaining DW signal is proportional to the intra-axonal volume. Since the spherical deconvolution used to compute FODs is a linear transformation, the integral of the (unnormalised) FOD is proportional to the DW signal summed over all gradient directions (and therefore the intra-axonal space). FOD amplitude along a specific direction (the AFD) is therefore proportional to the intra-axonal volume of axons aligned with this direction. In `recent work <http://www.ncbi.nlm.nih.gov/pubmed/26004503>`_, we have opted to compute the AFD per fibre population by computing the FOD integral within each FOD lobe (see `here <http://www.ncbi.nlm.nih.gov/pubmed/23238430>`_ for details). 

To enable robust quantitative comparisons of AFD across subjects (or AFD-derived quantities such as SIFT-filtered tractograms) there are three steps required:

#. **Bias field correction** to eliminate low frequency intensity inhomogeneities across the image.

#. **Global intensity normalisation** by normalising the median CSF or WM b=0 intensity across all subjects (see below for more details). This avoids the above noted issues with voxel-wise b=0 normalisation, such as CSF partial volume influencing restricted white matter DW signal (and therefore the AFD).   

#. **Use the same single fibre response function** in the spherical deconvolution for all subjects. This ensures differences in intra-axonal volume (and therefore DW signal) across subjects are detected as differences in the FOD amplitude (the AFD). See `the AFD paper <http://www.ncbi.nlm.nih.gov/pubmed/22036682>`_ for more details.


Pre-processsing steps
---------------------
Below is a list of recommended pre-processing steps for quantitative analysis using FOD images. Note that for all MRtrix scripts and commands, additional information on the command usage and available command-line options can be found by invoking the command with the :code:`-help` option. 


