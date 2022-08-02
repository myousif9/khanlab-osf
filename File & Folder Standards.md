# File and Folder Standards

**_This page is currently under construction_**

This document is designed to provide insight into naming conventions and structure of files and folders as stored on Graham. This document will be updated to reflect changes as the standard evolves. The goal is to reduce the number of repeated preprocessing involved when project data is shared amongst a number of users (eg. multiple people running prepdwi on the same dataset for their own use).

@[toc]

## Naming of Study Files

File names conform to the Brain Imaging Data Structure (BIDS). Provided below are links to help get started with BIDS. 

### BIDS 

* [Brain Imaging Data Structure](bids.neuroimaging.io)
* [BIDS Specification](bids.neuroimagnig.io/bids_spec.pdf)
* [BIDS Starter Kit](https://github.com/bids-standard/bids-starter-kit)

### BIDS Extensions

Additional links below are for BIDS extensions (eg. ASL, EEG, rs-fMRI, etc.) and are works in progress. They may provide guidance not specified in the current BIDS specification.

* [Common BIDS Derivatives](https://docs.google.com/document/d/1Wwc4A6Mow4ZPPszDIWfCUCRNstn7d_zzaWPcfcHmgI4/edit)
* [Arterial Spin Labelling](https://docs.google.com/document/d/15tnn5F10KpgHypaQJNNGiNKsni9035GtDqJzWqkkP6c/edit#heading=h.prrzvwchfio6)
* [Intracranial Electroencephalography](https://docs.google.com/document/d/1qMUkoaXzRMlJuOcfTYNr3fTsrl4SewWjffjMD5Ew6GY/edit)
* [Structural Preprocessing](https://docs.google.com/document/d/1YG2g4UkEio4t_STIBOqYOwneLEs1emHIXbGKynx7V0Y/edit#heading=h.mqkmyp254xh6)
* [Spaces and Mapping](https://docs.google.com/document/d/16CvBwVMAs0IMhdoKmlmcm3W8254dQmNARo-7HhE-lJU/edit#heading=h.mqkmyp254xh6)
* [Diffusion Weighted Imaging Derivatives](https://docs.google.com/document/d/1cQYBvToU7tUEtWMLMwXUCB_T8gebCotE1OczUpMYW60/edit#heading=h.mqkmyp254xh6)

The list above is only a small number of BIDS extension proposals which may be relevant. For a detailed list, please see the BIDS website.

## Atlases & Templates

__To be added__
* BIDS-like format
* Centralized storage?

## Folder Structure

Files are stored into specific folders according to two categories:
1. Unprocessed
    * **Read-only** folder located at `/project/6007967/cfmm-bids/<PI>/<Project>`
    
2. Preprocessed
    * **Writable** folder located at `/project/6007967/cfmm-derivatives/<PI>/<Project>`
    * Stores preprocessed data to be shared with the group (eg. fmriprep, prepdwi, etc.)
    * _bids-app exclusive or any processing to be shared?_

Preprocessed data should indicate the processing that was performed and the version of the BIDS-app used (`<bids_app>_<ver>`). If processing is dependent on a previous processing step, this can be indicated with an additional tag at the end of the folder structure (`<bids_app>_<ver>_<dependency>`)

Examples can be seen below:

1. Prepdwi (v0.0.11) is run on the unprocessed data acquired under project_1 under pi_1
    * `/project/6007967/cfmm-bids/pi_1/project_1` will store the unprocessed files according to BIDS
    * `/project/6007967/cfmm-derivatives/pi_1/project_1/prepdwi_v0.0.11/` will contain the files processed using prepdwi.

2. Again, prepdwi (v0.0.11) is run for project_1, but instead is run on the processed gradcorrected files
    * `/project/6007967/cfmm-bids/pi_1/project_1` still stores the unprocessed files according to bids
    * `/project/6007967/cfmm-derivatives/pi_1/project_1/gradcorrect_v0.0.2` is where the processed gradcorrected files are
    * `/project/6007967/cfmm-derivatives/pi_1/project_1/prepdwi_v0.0.11_gradcorrect` stores the prepdwi processed files following gradcorrect
        * Note the additional tag to indicate the gradcorrect dependency

## Folder Hierarchy

The folder hierarchy for cfmm-bids can be found in the example below: 

```
cfmm-bids
├── data
│   ├── PI_1
│   ├── PI_2
│   │   ├── Project_1
│   │   │   ├── sub-001
│   │   │   │   ├── anat 
│   │   │   │   │   ├── sub-001_T1w.nii.gz
│   │   │   │   │   ├── sub-001_T2w.nii.gz
│   │   │   │   ├── dwi
│   │   │   │── sub-002
│   │   │   │── sub-003
│   │   ├── Project_2
```

Below is an example of the folder hierarchy for cfmm-derivatives:
```
cfmm-derivatives
├── PI_1
├── PI_2
│   ├── Project_1
│   │   ├── <bids_app>_<ver>
│   │   ├── prepdwi_v0.0.11
│   │   │   ├── jobs
│   │   │   ├── prepdwi
│   │   │   │   ├── sub-001
│   │   │   │   │   ├── dwi
│   │   │   │   │   │   ├── uncorrected_denoise_unring_topup
│   │   │   │   │   │   │   ├── sub-001_dwi_brainmask.nii.gz
│   │   │   │   │   │   │   ├── sub-001_dwi_preproc.nii.gz
│   │   │   │   │   │   │   ├── sub-001_dwi_preproc.bval
│   │   │   │   │   │   │   ├── sub-001_dwi_preproc.bvec
│   │   │   │── work
│   │   │   │   ├── sub-002
│   ├── Project_2
```
