# Configure Jupyter Notebook for LibTorch (Torch on c++) with `xeus-cling`

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
#include <torch/torch.h>
#include <iostream>
```
```c++
//////////////////////////////////////////////////////////////
std::cout << "Deep Learning with PyTorch: A 60 Minute Blitz\n\n";
std::cout << "What is PyTorch?\n\n";

/*************************************************
 * TENSOR DECLARATION *
 *************************************************/
std::cout << "Tensors\n\n";

// Construct a 5x3 matrix, uninitialized:
auto x = torch::empty({5, 3});
std::cout << "x:\n" << x << '\n';

// Construct a randomly initialized matrix:
x = torch::rand({5, 3});
std::cout << "x:\n" << x << '\n';
```
```c++
// Device: Test CUDA in GPU
auto cuda_available = torch::cuda::is_available();
torch::Device device(cuda_available ? torch::kCUDA : torch::kCPU);
std::cout << (cuda_available ? "CUDA available. Training on GPU." : "Training on CPU.") << '\n';

auto x_gpu = torch::rand({5, 3}).to(device);
std::cout << "x_gpu:\n" << x_gpu << '\n';
```

---
#### References:

https://krshrimali.github.io/Setting-Up-Xeus-Cling-Libtorch-OpenCV/

https://gist.github.com/vfdev-5/911c4b12c1958648b19ebdacae84e798

https://github.com/pytorch/pytorch/blob/master/docs/libtorch.rst

https://blog.csdn.net/weixin_43742643/article/details/114156298











