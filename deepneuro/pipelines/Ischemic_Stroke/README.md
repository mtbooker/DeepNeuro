# Ischemic_Stroke

This module creates segmentations of ischemic stroke lesions given diffusion maps from DWI MR, and B0 maps as input volumes. These segmentations are created by deep neural networks trained on 991 patients from the Massachusetts General Hospital as part of the NIH-funded Heart-Brain Interactions in Human Acute Ischemic Stroke Study. The following pre-processing steps are included in module: Image Registration, and Zero-Mean normalization. This module was developed at the Quantitative Tumor Imaging Lab at the Martinos Center (MGH, MIT/Harvard HST).

## Table of Contents
- [Docker Usage](#docker-usage)
- [Python Docker Wrapper Usage](#python-docker-wrapper-usage)
- [Docker Example](#docker-example)
- [Citation](#citation)

## Docker Usage

The best way to use this module is with a Docker container. If you are not familiar with Docker, you can download it [here](https://docs.docker.com/engine/installation/) and read a tutorial on proper usage [here](https://docker-curriculum.com/).

Pull the Ischemic_Stroke Docker container from https://hub.docker.com/r/qtimlab/deepneuro_segment_ischemic_stroke/. Use the command "docker pull qtimlab/deepneuro_segment_ischemic_stroke".

You can then enter a command using the following template to generate a segmentation:

```
nvidia-docker run --rm -v [MOUNTED_DIRECTORY]:/INPUT_DATA qtimlab/deepneuro_segment_ischemic_stroke segment_ischemic_stroke pipeline -B0 <file> -DWI <file> -output_folder <directory> [-gpu_num <int> -registered -preprocessed -save_all_steps -save_preprocessed -segmentation_output <str>]
```

In order to use Docker, you must mount the directory containing all of your data and your output. All inputted filepaths must be relative to this mounted directory. For example, if you mounted the directory /home/my_users/data/, and wanted to input the file /home/my_users/data/patient_1/B0.nii.gz as a parameter, you should input /INPUT_DATA/patient_1/B0.nii.gz. Note that the Python wrapper for Docker in this module will adjust paths for you.

A brief explanation of this function's parameters follows:

| Parameter       | Documenation           |
| ------------- |-------------|
| -output_folder | A filepath to your output folder. Output segmentations will be placed here with filenames specified in -segmentation_output |
| -B0, DWI      | Filepaths to input MR modalities. Inputs can be either nifti files or DICOM folders. Note that DICOM folders should only contain one volume each.      |
| -segmentation_output | Optional. Name of output for enhancing tumor labels. Should not be a filepath, like '/home/user/segmentation.nii.gz', but just a name, like "segmentation.nii.gz"      |
| -gpu_num | Optional. Which CUDA GPU ID # to use. Defaults to 0, i.e. the first gpu. |
| -registered | If flagged, data is assumed to already have been registered into the same space, and skips that preprocessing step. |
| -preprocessed | If flagged, data is assumed to already have been entirely preprocessed by DeepNeuro, including intensity normalization. Only use if data has been passed through DeepNeuro already to ensure proper performance. |
| -save_all_steps | If flagged, intermediate volumes in between preprocessing steps will be saved in output_folder. |
| -save_preprocessed | If flagged, the final volumes after bias correction, resampling, and registration. |

## Python Docker Wrapper Usage

To avoid adjusting your  you may want to avoid using nvidia-docker directly. I've also created a python utility that wraps around the nvidia-docker command above, and is slightly easier to use. In order to use this utlity, you will need to clone this repository. ("git clone https://github.com/QTIM-Lab/DeepNeuro"), and install it ("python setup.py install", in the directory you cloned the repository).

Once you have installed the repository, you can use the following command on the command-line:

```
segment_ischemic_stroke docker_pipeline -B0 <file> -DWI <file> -output_folder <directory> [-gpu_num <int> -registered -preprocessed -save_all_steps -save_preprocessed
```

Parameters should be exactly the same as in the Docker use-case, except now you will not have to modify filepaths to be relative to the mounted folder.

## Docker Example

Let's say you stored some DICOM data on your computer at the path /home/my_user/Data/, and wanted to segment data located at /home/my_user/Data/Patient_1. The nvidia-docker command would look like this:

```
nvidia-docker run --rm -v /home/my_user/Data:/INPUT_DATA qtimlab/deepneuro_segment_ischemic_stroke segment_ischemic_stroke pipeline -B0 /INPUT_DATA/Patient_1/B0 -DWI /INPUT_DATA/Patient_1/DWI -output_folder /INPUT_DATA/Patient_1/Output_Folder
```

First, note that the "/INPUT_DATA" designation on the right-hand side of the "-v" option will never change. "INPUT_DATA" is a folder within the Docker container that will not change between runs.

Second, note that you will need to make sure that the left-hand side of the "-v" option is an absolute, rather than relative, path. For example "../Data/" and "~/Data/" will not work (relative path), but "/home/my_user/Data/" will work (absolute path, starting from the root directory).

Third, note that the folders you provide as arguments to the "segment_ischemic_stroke pipeline" command should be relative paths. This is because you are mounting, and thus renaming, a folder on your system to the "/INPUT_DATA" folder inside the Docker system. For example, if you were mounting the directory "/home/my_user/Data/" to "/INPUT_DATA", you should not provide the path "/home/my_user/Data/Patient_1/B0" as a parameter. Rather, you should provide the path "/INPUT_DATA/Patient_1/B0", as those parts of the path are within the scope of your mounted directory.

## Citation

Publication in preparation.
