# Neuroimaging Datasets for Visual Perception Reconstruction

This repository indexes open neuroimaging datasets suitable for reconstructing visual perception **from human fMRI data**. 

This guide is primarily aimed at researchers from AI and machine learning backgrounds who may not be familiar with neuroimaging methodology. Reconstruction from neuroimaging data has recently gained popularity at major AI conferences, but many approaches fall into **common traps** that are well known within neuroscience. These pitfalls can lead to misleading results, often due to misunderstandings about the nature of fMRI data or the limitations of datasets originally collected for other research questions. For a detailed discussion of such issues in recent reconstruction pipelines, see: Shirakawa, K. et al. (2025). [*Spurious reconstruction from brain activity*](https://www.sciencedirect.com/science/article/pii/S0893608025003946), _Neural Networks_ .

This is a **living document**. If you know of additional datasets or have corrections, please open an issue or start a discussion.

## Table of Contents
1. [Basics: Identification vs. Decoding vs. Reconstruction](#basic-distinction)
2. [Criteria for Reconstruction Datasets](#criteria-for-reconstruction-datasets)
3. [Image Stimulus Datasets](#image-stimulus-datasets)
   - [vim-1 – Naturalistic Grayscale Images](#vim-1--naturalistic-grayscale-images)
   - [10x10 Binary Patterns](#10x10-binary-patterns)
   - [BRAINS – Handwritten Characters](#brains--handwritten-characters)
   - [Generic Object Decoding](#generic-object-decoding)
   - [BOLD5000](#bold5000)
   - [NSD - Natural Scenes Dataset](#nsd)
   - [cneuromod-THINGS](#cneuromod-things)
   - (...)
4. [Video Stimulus Datasets](#video-stimulus-datasets)
   - [vim-2 – Naturalistic Video Clips](#vim-2--naturalistic-video-clips)
   - [studyforrest – Forrest Gump Movie](#studyforrest--forrest-gump-movie)
   - [cneuromod](#cneuromod)
   - [Doctor Who](#doctorwho)
   - (...)
5. [fMRI Data and Hemodynamic Delay](#fmri-data-and-hemodynamic-delay)


## Criteria for Reconstruction Datasets

This index contains dataset that are either suitable or have been commonly used for reconstruction research. 

Not every fMRI dataset that contains visual stimuli is suitable for reconstruction research. Many datasets were originally collected for other purposes (for example category decoding or representation analysis).

Here are suggested criteria to take into account when evaluating whether to use a particular neuroimaging dataset for your project.

- **Train–test independence**  
  Training and test stimuli should be visually and semantically distinct. If both sets contain highly similar images or share the same object categories or semantic clusters, models may simply learn to classify stimuli into known clusters instead of reconstructing novel images.

- **Stimulus diversity**  
  Reconstruction models must generalize beyond the training set. Datasets with limited semantic diversity restrict the feature space that models can learn. If your test set is truly independent (see first point), this limitation will typically become visible in reconstruction quality.

- **Repetitions and signal-to-noise ratio (SNR)**  
  fMRI signals are noisy. Many reconstruction-oriented datasets therefore include many repeated presentations of the test stimuli (_resampling_). Averaging across repetitions improves signal quality (SNR) and makes voxel-level patterns more reliable. Single-shot reconstruction is rarely seen outside invasive (implant) recording contexts and even there often produces substantially lower quality than reconstructions from resampled stimuli. If your pipeline is strong enough, however, it is worth trying.

- **Copyright and availability of stimulus files**  
  Reconstruction requires access to the original images or videos. Datasets where stimulus material cannot be redistributed (for example due to copyright restrictions) can be difficult to use in practice. Journals have occasionally required researchers to redraw copyrighted stimuli by hand, or only show CC0/public domain images, which is not ideal for presenting reconstruction results.

- **Smoothing in preprocessing**  
  Check the preprocessing description of the dataset. One standard preprocessing step from cognitive neuroscience is heavy spatial smoothing, i.e. essentially applying a Gaussian filter across the voxel matrix. This degrades the fine-grained spatial information that ML-based pattern analysis requires. For reconstruction projects it is particularly important that such voxel-level activity patterns remain intact. Note that as an ML researcher you may not easily be able to modify the extensive preprocessing and GLM pipeline yourself without assistance from someone with fMRI expertise, in order to exclude this step.


## fMRI Data and Hemodynamic Delay

![Canonical Hemodynamic Response Function](hrf.png)

fMRI records blood-oxygenation changes (the BOLD signal) following neural activity. fMRI data is organized as voxel-wise activity measurements in a 3D volume (voxels are 3D pixels), which – unlike many other neuroimaging modalities – makes it straightforward to use with standard machine learning methods.

Among non-invasive neuroimaging modalities, fMRI offers the best spatial resolution, which enables detailed localization of brain activity. Unlike invasive recording methods, it can also cover the whole visual system and the whole brain.

The BOLD response typically peaks ~4–6 seconds after neural firing and returns to baseline after ~10–12 seconds. This means that the fMRI signal at time t reflects neural activity from several seconds earlier, i.e.: from the stimulus presented several seconds earlier. The image shows the canonical hemodynamic response function (HRF) presented in neuroscience literature and implemented in many frameworks. In practice, the exact shape and timing vary across voxels depending on vascularization and other physiological factors.

In large-scale visual neuroimaging experiments many stimuli are presented in rapid succession. To keep participants engaged and prevent fatigue, stimulus presentation is often continuous, which leads to substantial temporal overlap between successive HRFs.

This delay and overlap must be accounted for when aligning stimuli and brain activity.

For static image datasets this is usually already handled in the public releases, using a general linear model (GLM) that estimates the response to each stimulus.

For video datasets the situation is more complicated, because the stimulus is continuous. The HRF delay must therefore be taken into account when modeling, and there is currently no widely agreed-upon standard approach.


## Citation

If you found this index useful and want to cite it, please use:

Seeliger, K. (2026). Neuro-Visual Reconstruction Dataset Index.  
Zenodo. https://doi.org/xxxx
