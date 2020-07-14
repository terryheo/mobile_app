# Prebuilt MLPerf Mobile App

This directory contains prebuilt versions of the MLPerf (Android) app, for
convenience.

*Please note that this app is not official yet and the integration with LoadGen
isn't quite complete yet.*

## Getting Started

Until the app is available in the
[Play Store](https://play.google.com/store?hl=en_US), it requires manual
installation from a host machine. Due to restrictions in dataset redistribution,
some amount of manual retrieval, processing and packaging is required to use
certain task datasets for accuracy evaluation. We're actively investigating how
we can bundle the necessary datasets directly in the app.

### App installation

Install the app via `adb` as follows:

```
adb install ${ROOT_DIR}/prebuilt/MLPerf_0.7_20200714.apk
```

The prebuilt APK contains libraries for 32 and 64-bit ARM devices, as well as
for 32-bit x86 devices (primarily for testing).

If you're primarily interested in testing inference latency, skip directly to
the [App Execution](#app-execution) section. The app can run MLPerf latency
measurements without the need for full dataset installation.

### Dataset installation

A tasks-specific dataset is required required for measuring accuracy.

This currently requires some manual work to prepare and distribute the relevant
datasets. Note that the full datasets can be extremely large; a subset of the
full dataset can be prepared to give a useful proxy for task-specific accuracy.
When prepared, each task directory should be pushed to
`/sdcard/mlperf_datasets/...`.

#### Imagenet (Classification)

The app evaluates Image Classification via MobileNet V1 (float & quantized),
using any subset of images used in the
[ILSVRC 2012 image classification task](http://www.image-net.org/download-images/).

To download the 2012 dataset, you will need to do the following:

*   Open the
    [ImageNet downloads link](http://www.image-net.org/download-images),
    creating an ImageNet account if you have not done so already.
*   Navigate to the 2012 downloads page under the **`Download links to ILSVRC
    image data`** section.
*   Download the **`Validation images (all tasks)`** archive.
*   Create a staging directory for the dataset, call it
    `${WORKING_DIR}/imagenet`.
*   From the downloaded archive, extract the images into a
    `${WORKING_DIR}/imagenet/img` directory.

The full set of validation images is quite large, over 6GB. This can take an
extremely long time to process, so it is *highly* recommended that you use a
smaller subset of images. We recommend leaving ~300 images in
`${WORKING_DIR}/imagenet/img`. The resulting folder contents should appear like:

*   `imagenet/img` : `folder` \
    Contains the set of images to evaluate over. This could be any subset of the
    ILSVRC 2012 evaluation dataset (strongly recommend using <300 for test/dev
    purposes).

The classification model and validation label files are already in the app, for
convenience. Once the data has been prepared, push the resulting `imagenet`
folder to `/sdcard/mlperf_datasets/imagenet`.

#### Coco (Detection)

The app evaluates Object Detection using any subset of images used in
[COCO 2017 dataset](http://cocodataset.org/#download).

To prepare the dataset for object detection you need to do the following:

*   Download the 2017 Val images from above website.
*   Extract the images to `${WORKING_DIR}/coco/img` directory.

The resulting folder contents should appear like:

*   `coco/img` : `folder` \
    Contains the set of images to evaluate over. This could be any subset of the
    COCO 2017 evaluation dataset.

Once the data has been prepared push the resulting `coco` folder to
`/sdcard/mlperf_datasets/coco`.

## <a name="app-execution"></a> App Execution

After the app has been installed, and the optional dataset(s), simply open
**`MLPerf Inference`** from the Android launcher. The app UI allows selection of
supported tasks, and execution of the supported models (float/quantized) for
each task. If a dataset has been pushed for the task, the task-specific accuracy
will be measured, otherwise it will only report latency results.

The user can also configure runtime execution with the following options:

*   TensorFlow Lite Accelerator:
    *   None - Default (CPU) backend for TensorFlow Lite
    *   GPU - Enables use of the GPU (via OpenGL/OpennCL)
    *   NNAPI - Enables use of
        [Android's NN API](https://developer.android.com/ndk/guides/neuralnetworks)
*   Number of threads - for CPU execution
