# Neuroimaging Datasets for Visual Perception Reconstruction

This repository indexes open neuroimaging datasets for reconstructing visual perception **from human fMRI data**. 

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
   - [NSD - Natural Scenes Dataset](#nsd-natural-scenes-dataset)
   - [cneuromod-THINGS](#cneuromod-things)
   - (...)
4. [Video Stimulus Datasets](#video-stimulus-datasets)
   - [vim-2 – Naturalistic Video Clips](#vim-2--naturalistic-video-clips)
   - [studyforrest – Forrest Gump Movie](#studyforrest--forrest-gump-movie)
   - [cneuromod](#cneuromod)
   - [Doctor Who](#doctorwho)
   - (...)
5. [fMRI Data and Hemodynamic Delay](#fmri-data-and-hemodynamic-delay)


## Basics: Identification vs. Decoding vs. Reconstruction

These terms are now often conflated. In foundational reconstruction and decoding literature their separation has been strict for good reasons: the tasks differ in difficulty, failure modes, what can be concluded from a result and what a method can realistically achieve. 

**True reconstruction requires potential to generalize to stimuli and categories that were not present during training.** If the correct answer is (also implicitly) selected from a predefined candidate category or image set, the wording is _decoding_ or _identification_, not reconstruction. 

| Neuroscience term | ML framing | Search space | Difficulty |
|---|---|---|---|
| **Decoding** | classification | closed label/category set | easy |
| **Identification** | retrieval | finite set of images | moderate |
| **Reconstruction** | generative inverse problem | infinite, open set of perception | hard |

- **Decoding (category level)**  
 Decoding refers to predicting (classifying) pre-defined labels or cognitive states from brain activity patterns. This type of classification has long been used for [neuroscientific insight](https://www.cell.com/neuron/fulltext/S0896-6273(15)00432-8). In multivariate pattern analysis (MVPA), voxel activity patterns are treated as feature vectors and classifiers are trained to distinguish experimental conditions (for example risky vs. safe decision, add vs. subtract, rule A vs. rule B).

  Decoding is scientifically useful because it tests whether information about a stimulus or mental state is present in a brain region. However, it does not imply that the full visual content can be recovered. 

  Many recent papers labeled as “reconstruction” are effectively performing n-way decoding: they decode a category and then use a generative model to produce a visually plausible sample inside the predicted class. This can look convincing, but remains a restricted classification problem. Similar n-way decoding setups have long been standard in the MVPA literature and can achieve quite high performance when the candidate set is limited.

- **Identification (stimulus level)**  
  Identification refers to selecting which stimulus was shown from a finite set of candidates based on brain activity. For example, given N possible images, the task is to determine which one produced the observed brain response. While interesting and useful, particularly within large stimulus sets, it remains a selection problem within a predefined set.

- **Reconstruction (open-set stimulus inference)**  
  Reconstruction aims to rebuild the stimulus itself from brain activity, generalizing to **novel stimuli outside the training set**. Because the space of possible visual stimuli is essentially infinite, this is a substantially harder problem than decoding or identification.

  A long-term motivation for reconstruction research is the possibility of recovering **internally generated visual experiences**, such as mental imagery or even [**dreams**](https://www.science.org/doi/10.1126/science.1234330). In these settings there is obviously no predefined candidate set of images to choose from.


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
  Reconstruction requires access to the original images or videos. Datasets where stimulus material cannot be redistributed (for example due to copyright restrictions) can be difficult to use in practice. Journals have occasionally required researchers to redraw copyrighted original stimuli by hand, or only show CC0/public domain images, which is not ideal for presenting reconstruction results.

- **Smoothing in preprocessing**  
  Check the preprocessing description of the dataset. One standard preprocessing step from cognitive neuroscience is heavy spatial smoothing, i.e. essentially applying a Gaussian filter across the voxel matrix. This degrades the fine-grained spatial information that ML-based pattern analysis requires. For reconstruction projects it is particularly important that such voxel-level activity patterns remain intact. Note that as an ML researcher you may not easily be able to modify the extensive preprocessing and GLM pipeline yourself without assistance from someone with fMRI expertise, in order to exclude this step.

- **Fixation**  
  Most visual neuroimaging experiments require participants to **fixate on a point in the center of the screen** during stimulus presentation. This is important because large parts of the visual system are retinotopically organized (a distorted retina-reflecting map). If participants freely move their eyes, the same image stimulates different parts of this cortical map. Eye movements can introduce strong confounds: models may end up decoding eye position on the cortical map and not stimulus-related cortical patterns. There is debate about the strict necessity of fixation though. It also reduces natural viewing behavior and may suppress activity in higher-level visual areas. In general, reconstruction on free-viewing datasets should be approached with caution.

## Image Stimulus Datasets

### vim-1 - Naturalistic Grayscale Images

| Attribute | Details |
|---|---|
| **Stimulus type** | naturalistic grayscale images |
| **Stimuli** | 1750 training images, 120 test images |
| **Repetitions** | train: 2x, test: 13x |
| **Subjects** | 2 |
| **Visual coverage** | circularly masked images |
| **Brain regions** | visual cortex V1-V4, LatOcc, extrastriate (unspecified) regions |
| **Main publication** | [Kay et al., 2008](https://doi.org/10.1038/nature06713) |
| **Data access** | [CRCNS dataset page](https://crcns.org/data-sets/vc/vim-1) |

**Experiment**

Two participants viewed natural grayscale images while maintaining fixation. Images were presented inside a circular aperture. 

**Notes**
- The **“MNIST of computational visual neuroscience”** because of its small size, clean design, high number (13) of test repetitions, and wide use in encoding and reconstruction. Many reconstruction and encoding model projects use this dataset as a first test.
- The dataset was originally used to demonstrate voxel-wise encoding models and do stimulus identification by inverting them.
- Easy-to-use ROI masks for early visual cortex, mid/higher level regions also covered.
- Broad semantic categories.


### NSD - Natural Scenes Dataset

| Attribute | Details |
|---|---|
| **Stimulus type** | natural color images (MS COCO) |
| **Stimuli** | ~73,000 unique images total |
| **Images per subject** | ~10,000 unique images |
| **Repetitions** | 3x both train/test |
| **Subjects** | 8 |
| **Scanner** | 7T fMRI (high-res) |
| **Brain coverage** | whole brain |
| **Main publication** | [Allen et al., 2022](https://doi.org/10.1038/s41593-021-00962-x) |
| **Data access** | https://naturalscenesdataset.org |

**Experiment**

Participants viewed natural images from the **MS COCO dataset** during long-term scanning sessions (30–40 sessions per participant) while maintaining fixation. Each subject saw ~10,000 unique images repeated three times. About 1,000 images are shared across all subjects for cross-subject analysis. 

**Notes**

- One of the largest and highest-quality human fMRI datasets currently available.
- Widely used for encoding models, representational analysis, and large-scale brain–DNN comparisons.
- Provides whole-brain coverage, high spatial resolution (7T), and extensive metadata.
- Includes additional resources such as an [imagery experiment](https://arxiv.org/abs/2506.06898) and [synthetic images](https://www.nature.com/articles/s41467-026-69345-9).

**Note for reconstruction research**

The dataset was _not originally designed for reconstruction experiments_. The standard train/test split contains strong semantic clustering and substantial similarity between training and test images within the same MS COCO categories. This can inflate apparent reconstruction performance (see [Shirakawa et al., 2025](https://www.sciencedirect.com/science/article/pii/S0893608025003946)). For reconstruction studies it is advisable to create alternative splits where test stimuli contain categories not present during training, or where semantic and visual overlap between training and test images is minimized.


## Video Stimulus Datasets

### vim-2 – Naturalistic Video Clips

| Attribute | Details |
|---|---|
| **Stimulus type** | naturalistic videos (movie clips) |
| **Stimuli** | ~7200 training timepoints, 540 test timepoints |
| **Repetitions** | train: 1x, test: 10x |
| **Subjects** | 3 |
| **Brain regions** | visual cortex |
| **Main publication** | [Nishimoto et al., 2011](https://doi.org/10.1016/j.cub.2011.08.031) |
| **Data access** | [CRCNS dataset page](https://crcns.org/data-sets/vc/vim-2) |

**Experiment**

Three participants viewed natural movie clips while maintaining fixation. Videos were presented in grayscale and slowed to half speed to better match the hemodynamic delay of the BOLD signal.

**Notes**

- Reconstruction from Nishimoto 2011 (YouTube link)  
[![Nishimoto 2011 reconstruction video](https://img.youtube.com/vi/nsjDnYxJ0bo/0.jpg)](https://www.youtube.com/watch?v=nsjDnYxJ0bo)
- The dataset was originally used to demonstrate encoding models with motion energy features. Reconstruction was done to demonstrate the power of these features to represent brain activity. 
- Similar high quality and easy to use as vim-1.
- Some train–test overlap has been reported, see [Shirakawa et al., 2025](https://www.sciencedirect.com/science/article/pii/S0893608025003946).

## fMRI Data and Hemodynamic Delay

![Canonical Hemodynamic Response Function](hrf.png)

fMRI records blood-oxygenation changes (the BOLD signal) following neural activity. fMRI data is organized as voxel-wise activity measurements in a 3D volume (voxels are 3D pixels), which – unlike many other neuroimaging modalities – makes it straightforward to use with standard machine learning methods.

Among non-invasive neuroimaging modalities, fMRI offers the best spatial resolution, which enables detailed localization of brain activity. Unlike invasive recording methods, it can also cover the whole visual system and the whole brain and can easily be recorded in healthy humans.

The BOLD response typically peaks ~4–6 seconds after neural firing and returns to baseline after ~10–12 seconds. This means that the fMRI signal at time t reflects neural activity from several seconds earlier, i.e.: from the stimulus presented several seconds earlier. The image shows the canonical hemodynamic response function (HRF) presented in neuroscience literature and implemented in many frameworks. In practice, the exact shape and timing vary across voxels depending on vascularization and other physiological factors.

In large-scale visual neuroimaging experiments many stimuli are presented in rapid succession. To keep participants engaged and prevent fatigue, stimulus presentation is often continuous, which leads to substantial temporal overlap between successive HRFs.

This delay and overlap must be accounted for when aligning stimuli and brain activity.

For static image datasets this is usually already handled in the public releases, using a general linear model (GLM) that estimates the response to each stimulus.

For video datasets the situation is more complicated, because the stimulus is continuous. The HRF delay must therefore be taken into account when modeling, and there is currently no widely agreed-upon standard approach.


## Citation

If you found this index useful and want to cite it, please use:

Seeliger, K. (2026). Neuro-Visual Reconstruction Dataset Index.  
Zenodo. https://doi.org/xxxx
