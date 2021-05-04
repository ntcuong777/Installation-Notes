
﻿# Configure Jupyter Notebook for LibTorch (Torch on c++) with `xeus-cling`

**Requirements**: Anaconda

1. We create a new conda environment, named `libtorch`:
```
$ conda create --name libtorch python=3.6
$ conda activate libtorch
```
2. Install `xeus-cling` and `jupyter-notebook`
```
$ conda install xeus-cling -c conda-forge
$ conda install notebook -c conda-forge 
```
3. Install kernel for jupyter notebook:
```
$ jupyter kernelspec install <path-to-conda-env-libtorch>/share/jupyter/kernels/xcpp11 --sys-prefix
$ jupyter kernelspec install <path-to-conda-env-libtorch>/share/jupyter/kernels/xcpp14 --sys-prefix
$ jupyter kernelspec install <path-to-conda-env-libtorch>/share/jupyter/kernels/xcpp17 --sys-prefix
```

4. Download prebuilt libtorch **with cxx11 ABI option** (the lastest version may be updated). Here, I use Torch v1.7.1 with CUDA-10.1 (I referenced from [here](https://blog.csdn.net/weixin_43742643/article/details/114156298)):

| Platform       | Download |
|-----------|----------|
| CPU |[1.7.1](https://download.pytorch.org/libtorch/cpu/libtorch-cxx11-abi-shared-with-deps-1.7.1%2Bcpu.zip) ---- [v1.8.0](https://download.pytorch.org/libtorch/cpu/libtorch-cxx11-abi-shared-with-deps-1.8.0%2Bcpu.zip) ---- [v1.8.1](https://download.pytorch.org/libtorch/cpu/libtorch-cxx11-abi-shared-with-deps-1.8.1%2Bcpu.zip)|
| CUDA-10.1 |[1.7.1](https://download.pytorch.org/libtorch/cu101/libtorch-cxx11-abi-shared-with-deps-1.7.1%2Bcu101.zip) ---- [v1.8.0](https://download.pytorch.org/libtorch/cu101/libtorch-cxx11-abi-shared-with-deps-1.8.0%2Bcu101.zip) ---- [v1.8.1](https://download.pytorch.org/libtorch/cu101/libtorch-cxx11-abi-shared-with-deps-1.8.1%2Bcu101.zip)|
| CUDA-10.2 |[v1.8.1](https://download.pytorch.org/libtorch/cu102/libtorch-cxx11-abi-shared-with-deps-1.8.1%2Bcu102.zip)|
| CUDA-11.0 |[v1.7.1](https://download.pytorch.org/libtorch/cu110/libtorch-cxx11-abi-shared-with-deps-1.7.1%2Bcu110.zip)|
| CUDA-11.1 |[v1.8.0](https://download.pytorch.org/libtorch/cu110/libtorch-cxx11-abi-shared-with-deps-1.8.0%2Bcu110.zip) ---- [v1.8.1](https://download.pytorch.org/libtorch/cu111/libtorch-cxx11-abi-shared-with-deps-1.8.1%2Bcu111.zip)|

5. Unzip libtorch to `<libtorch-root-dir>`
6. Open a notebook with `C++14` :
```
$ jupyter notebook
```	
7. Add these lines in first cells and run the cell:

```c++
// If you want to add include path
#pragma cling add_include_path("<libtorch-root-dir>/include")
#pragma cling add_include_path("<libtorch-root-dir>/include/torch/csrc/api/include")

// If you want to add library path
#pragma cling add_library_path("<libtorch-root-dir>/lib")

// If you want to load library for CPU (default)
#pragma cling load("libc10.so")
#pragma cling load("libcaffe2_detectron_ops_gpu.so")
#pragma cling load("libcaffe2_module_test_dynamic.so")
#pragma cling load("libcaffe2_observers.so")
#pragma cling load("libfmt.so")
#pragma cling load("libfmt.so.7")
#pragma cling load("libfmt.so.7.0.3")
#pragma cling load("libgomp-75eea7e8.so.1")
#pragma cling load("libjitbackend_test.so")
#pragma cling load("libprocess_group_agent.so")
#pragma cling load("libshm.so")
#pragma cling load("libtensorpipe_agent.so")
#pragma cling load("libtorch.so")
#pragma cling load("libtorchbind_test.so")
#pragma cling load("libtorch_cpu.so")
#pragma cling load("libtorch_global_deps.so")
```
```c++
// If you use GPU, add more libraries
#pragma cling load("libc10_cuda.so")
#pragma cling load("libcaffe2_nvrtc.so")
#pragma cling load("libcudart-1b201d85.so.10.1")
#pragma cling load("libnvrtc-5e8a26c9.so.10.1")
#pragma cling load("libnvrtc-builtins.so")
#pragma cling load("libnvToolsExt-3965bdd0.so.1")
#pragma cling load("libtorch_cuda.so")
#pragma cling load("libc10d_cuda_test.so")
```

```c++
// We add include path and library path for OpenCV
#pragma cling add_include_path("/usr/local/include/opencv4")
#pragma cling add_include_path("/usr/local/include/opencv4/opencv2")
#pragma cling add_library_path("/usr/local/lib")

// We load lib for OpenCV
#pragma cling load("libopencv_alphamat.so")
#pragma cling load("libopencv_alphamat.so.4.5")
#pragma cling load("libopencv_alphamat.so.4.5.2")
#pragma cling load("libopencv_aruco.so")
#pragma cling load("libopencv_aruco.so.4.5")
#pragma cling load("libopencv_aruco.so.4.5.2")
#pragma cling load("libopencv_bgsegm.so")
#pragma cling load("libopencv_bgsegm.so.4.5")
#pragma cling load("libopencv_bgsegm.so.4.5.2")
#pragma cling load("libopencv_bioinspired.so")
#pragma cling load("libopencv_bioinspired.so.4.5")
#pragma cling load("libopencv_bioinspired.so.4.5.2")
#pragma cling load("libopencv_calib3d.so")
#pragma cling load("libopencv_calib3d.so.4.5")
#pragma cling load("libopencv_calib3d.so.4.5.2")
#pragma cling load("libopencv_ccalib.so")
#pragma cling load("libopencv_ccalib.so.4.5")
#pragma cling load("libopencv_ccalib.so.4.5.2")
#pragma cling load("libopencv_core.so")
#pragma cling load("libopencv_core.so.4.5")
#pragma cling load("libopencv_core.so.4.5.2")
#pragma cling load("libopencv_cudaarithm.so")
#pragma cling load("libopencv_cudaarithm.so.4.5")
#pragma cling load("libopencv_cudaarithm.so.4.5.2")
#pragma cling load("libopencv_cudabgsegm.so")
#pragma cling load("libopencv_cudabgsegm.so.4.5")
#pragma cling load("libopencv_cudabgsegm.so.4.5.2")
#pragma cling load("libopencv_cudafeatures2d.so")
#pragma cling load("libopencv_cudafeatures2d.so.4.5")
#pragma cling load("libopencv_cudafeatures2d.so.4.5.2")
#pragma cling load("libopencv_cudafilters.so")
#pragma cling load("libopencv_cudafilters.so.4.5")
#pragma cling load("libopencv_cudafilters.so.4.5.2")
#pragma cling load("libopencv_cudaimgproc.so")
#pragma cling load("libopencv_cudaimgproc.so.4.5")
#pragma cling load("libopencv_cudaimgproc.so.4.5.2")
#pragma cling load("libopencv_cudalegacy.so")
#pragma cling load("libopencv_cudalegacy.so.4.5")
#pragma cling load("libopencv_cudalegacy.so.4.5.2")
#pragma cling load("libopencv_cudaobjdetect.so")
#pragma cling load("libopencv_cudaobjdetect.so.4.5")
#pragma cling load("libopencv_cudaobjdetect.so.4.5.2")
#pragma cling load("libopencv_cudaoptflow.so")
#pragma cling load("libopencv_cudaoptflow.so.4.5")
#pragma cling load("libopencv_cudaoptflow.so.4.5.2")
#pragma cling load("libopencv_cudastereo.so")
#pragma cling load("libopencv_cudastereo.so.4.5")
#pragma cling load("libopencv_cudastereo.so.4.5.2")
#pragma cling load("libopencv_cudawarping.so")
#pragma cling load("libopencv_cudawarping.so.4.5")
#pragma cling load("libopencv_cudawarping.so.4.5.2")
#pragma cling load("libopencv_cudev.so")
#pragma cling load("libopencv_cudev.so.4.5")
#pragma cling load("libopencv_cudev.so.4.5.2")
#pragma cling load("libopencv_datasets.so")
#pragma cling load("libopencv_datasets.so.4.5")
#pragma cling load("libopencv_datasets.so.4.5.2")
#pragma cling load("libopencv_dnn.so")
#pragma cling load("libopencv_dnn.so.4.5")
#pragma cling load("libopencv_dnn.so.4.5.2")
#pragma cling load("libopencv_dnn_objdetect.so")
#pragma cling load("libopencv_dnn_objdetect.so.4.5")
#pragma cling load("libopencv_dnn_objdetect.so.4.5.2")
#pragma cling load("libopencv_dnn_superres.so")
#pragma cling load("libopencv_dnn_superres.so.4.5")
#pragma cling load("libopencv_dnn_superres.so.4.5.2")
#pragma cling load("libopencv_dpm.so")
#pragma cling load("libopencv_dpm.so.4.5")
#pragma cling load("libopencv_dpm.so.4.5.2")
#pragma cling load("libopencv_face.so")
#pragma cling load("libopencv_face.so.4.5")
#pragma cling load("libopencv_face.so.4.5.2")
#pragma cling load("libopencv_features2d.so")
#pragma cling load("libopencv_features2d.so.4.5")
#pragma cling load("libopencv_features2d.so.4.5.2")
#pragma cling load("libopencv_flann.so")
#pragma cling load("libopencv_flann.so.4.5")
#pragma cling load("libopencv_flann.so.4.5.2")
#pragma cling load("libopencv_freetype.so")
#pragma cling load("libopencv_freetype.so.4.5")
#pragma cling load("libopencv_freetype.so.4.5.2")
#pragma cling load("libopencv_fuzzy.so")
#pragma cling load("libopencv_fuzzy.so.4.5")
#pragma cling load("libopencv_fuzzy.so.4.5.2")
#pragma cling load("libopencv_gapi.so")
#pragma cling load("libopencv_gapi.so.4.5")
#pragma cling load("libopencv_gapi.so.4.5.2")
#pragma cling load("libopencv_hdf.so")
#pragma cling load("libopencv_hdf.so.4.5")
#pragma cling load("libopencv_hdf.so.4.5.2")
#pragma cling load("libopencv_hfs.so")
#pragma cling load("libopencv_hfs.so.4.5")
#pragma cling load("libopencv_hfs.so.4.5.2")
#pragma cling load("libopencv_highgui.so")
#pragma cling load("libopencv_highgui.so.4.5")
#pragma cling load("libopencv_highgui.so.4.5.2")
#pragma cling load("libopencv_img_hash.so")
#pragma cling load("libopencv_img_hash.so.4.5")
#pragma cling load("libopencv_img_hash.so.4.5.2")
#pragma cling load("libopencv_imgcodecs.so")
#pragma cling load("libopencv_imgcodecs.so.4.5")
#pragma cling load("libopencv_imgcodecs.so.4.5.2")
#pragma cling load("libopencv_imgproc.so")
#pragma cling load("libopencv_imgproc.so.4.5")
#pragma cling load("libopencv_imgproc.so.4.5.2")
#pragma cling load("libopencv_intensity_transform.so")
#pragma cling load("libopencv_intensity_transform.so.4.5")
#pragma cling load("libopencv_intensity_transform.so.4.5.2")
#pragma cling load("libopencv_line_descriptor.so")
#pragma cling load("libopencv_line_descriptor.so.4.5")
#pragma cling load("libopencv_line_descriptor.so.4.5.2")
#pragma cling load("libopencv_mcc.so")
#pragma cling load("libopencv_mcc.so.4.5")
#pragma cling load("libopencv_mcc.so.4.5.2")
#pragma cling load("libopencv_ml.so")
#pragma cling load("libopencv_ml.so.4.5")
#pragma cling load("libopencv_ml.so.4.5.2")
#pragma cling load("libopencv_objdetect.so")
#pragma cling load("libopencv_objdetect.so.4.5")
#pragma cling load("libopencv_objdetect.so.4.5.2")
#pragma cling load("libopencv_optflow.so")
#pragma cling load("libopencv_optflow.so.4.5")
#pragma cling load("libopencv_optflow.so.4.5.2")
#pragma cling load("libopencv_phase_unwrapping.so")
#pragma cling load("libopencv_phase_unwrapping.so.4.5")
#pragma cling load("libopencv_phase_unwrapping.so.4.5.2")
#pragma cling load("libopencv_photo.so")
#pragma cling load("libopencv_photo.so.4.5")
#pragma cling load("libopencv_photo.so.4.5.2")
#pragma cling load("libopencv_plot.so")
#pragma cling load("libopencv_plot.so.4.5")
#pragma cling load("libopencv_plot.so.4.5.2")
#pragma cling load("libopencv_quality.so")
#pragma cling load("libopencv_quality.so.4.5")
#pragma cling load("libopencv_quality.so.4.5.2")
#pragma cling load("libopencv_rapid.so")
#pragma cling load("libopencv_rapid.so.4.5")
#pragma cling load("libopencv_rapid.so.4.5.2")
#pragma cling load("libopencv_reg.so")
#pragma cling load("libopencv_reg.so.4.5")
#pragma cling load("libopencv_reg.so.4.5.2")
#pragma cling load("libopencv_rgbd.so")
#pragma cling load("libopencv_rgbd.so.4.5")
#pragma cling load("libopencv_rgbd.so.4.5.2")
#pragma cling load("libopencv_saliency.so")
#pragma cling load("libopencv_saliency.so.4.5")
#pragma cling load("libopencv_saliency.so.4.5.2")
#pragma cling load("libopencv_sfm.so")
#pragma cling load("libopencv_sfm.so.4.5")
#pragma cling load("libopencv_sfm.so.4.5.2")
#pragma cling load("libopencv_shape.so")
#pragma cling load("libopencv_shape.so.4.5")
#pragma cling load("libopencv_shape.so.4.5.2")
#pragma cling load("libopencv_stereo.so")
#pragma cling load("libopencv_stereo.so.4.5")
#pragma cling load("libopencv_stereo.so.4.5.2")
#pragma cling load("libopencv_stitching.so")
#pragma cling load("libopencv_stitching.so.4.5")
#pragma cling load("libopencv_stitching.so.4.5.2")
#pragma cling load("libopencv_structured_light.so")
#pragma cling load("libopencv_structured_light.so.4.5")
#pragma cling load("libopencv_structured_light.so.4.5.2")
#pragma cling load("libopencv_superres.so")
#pragma cling load("libopencv_superres.so.4.5")
#pragma cling load("libopencv_superres.so.4.5.2")
#pragma cling load("libopencv_surface_matching.so")
#pragma cling load("libopencv_surface_matching.so.4.5")
#pragma cling load("libopencv_surface_matching.so.4.5.2")
#pragma cling load("libopencv_text.so")
#pragma cling load("libopencv_text.so.4.5")
#pragma cling load("libopencv_text.so.4.5.2")
#pragma cling load("libopencv_tracking.so")
#pragma cling load("libopencv_tracking.so.4.5")
#pragma cling load("libopencv_tracking.so.4.5.2")
#pragma cling load("libopencv_video.so")
#pragma cling load("libopencv_video.so.4.5")
#pragma cling load("libopencv_video.so.4.5.2")
#pragma cling load("libopencv_videoio.so")
#pragma cling load("libopencv_videoio.so.4.5")
#pragma cling load("libopencv_videoio.so.4.5.2")
#pragma cling load("libopencv_videostab.so")
#pragma cling load("libopencv_videostab.so.4.5")
#pragma cling load("libopencv_videostab.so.4.5.2")
#pragma cling load("libopencv_wechat_qrcode.so")
#pragma cling load("libopencv_wechat_qrcode.so.4.5")
#pragma cling load("libopencv_wechat_qrcode.so.4.5.2")
#pragma cling load("libopencv_xfeatures2d.so")
#pragma cling load("libopencv_xfeatures2d.so.4.5")
#pragma cling load("libopencv_xfeatures2d.so.4.5.2")
#pragma cling load("libopencv_ximgproc.so")
#pragma cling load("libopencv_ximgproc.so.4.5")
#pragma cling load("libopencv_ximgproc.so.4.5.2")
#pragma cling load("libopencv_xobjdetect.so")
#pragma cling load("libopencv_xobjdetect.so.4.5")
#pragma cling load("libopencv_xobjdetect.so.4.5.2")
#pragma cling load("libopencv_xphoto.so")
#pragma cling load("libopencv_xphoto.so.4.5")
#pragma cling load("libopencv_xphoto.so.4.5.2")
```

```c++
#include <torch/torch.h>

#include <opencv2/core.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>

#include <iostream>
```
```c++
/*************************************************
 * TENSOR DECLARATION *
 *************************************************/
std::cout << "Tensors\n\n";

// Construct a randomly initialized matrix:
auto tensor_cpu = torch::rand({5, 3});
std::cout << "tensor_cpu:\n" << tensor_cpu << "\n\n";

// Device: Test CUDA in GPU
auto cuda_available = torch::cuda::is_available();
torch::Device device(cuda_available ? torch::kCUDA : torch::kCPU);
std::cout << (cuda_available ? "CUDA available. Training on GPU." : "Training on CPU.") << "\n\n";

auto tensor_gpu = tensor_cpu.to(device);
std::cout << "tensor_gpu:\n" << tensor_gpu << '\n';
```
```c++
using namespace cv;

// test opencv read and show image
std::string image_path = "image.jpeg";
Mat img = imread(image_path, IMREAD_COLOR);
if(img.empty())
{
    std::cout << "Could not read the image: " << image_path << std::endl;
    return 1;
}
imshow("Display window", img);
int k = waitKey(0); // Wait for a keystroke in the window
destroyAllWindows();
```

---
#### References:

https://krshrimali.github.io/Setting-Up-Xeus-Cling-Libtorch-OpenCV/

https://gist.github.com/vfdev-5/911c4b12c1958648b19ebdacae84e798

https://github.com/pytorch/pytorch/blob/master/docs/libtorch.rst

https://blog.csdn.net/weixin_43742643/article/details/114156298











