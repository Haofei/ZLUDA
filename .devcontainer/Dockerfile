FROM nvidia/cuda:12.8.1-base-ubuntu24.04

RUN DEBIAN_FRONTEND=noninteractive apt-get update -y && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    wget \
    build-essential \
    cmake \
    ninja-build \
    python3 \
    ripgrep \
    git \
    ltrace \
    # required by llvm 17
    lsb-release software-properties-common gnupg

ARG LLVM_VERSION=17
RUN wget https://apt.llvm.org/llvm.sh && \
    chmod +x llvm.sh && \
    ./llvm.sh ${LLVM_VERSION}

# Feel free to change to a newer version if you have a newer verison on your host
ARG CUDA_PKG_VERSION=12-8
# Docker <-> host  driver version compatiblity is newer host <-> older docker
# We don't care about a specific driver version, so pick oldest 5XX compatible
ARG CUDA_DRIVER=570
RUN wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/libcudnn8_8.9.7.29-1+cuda12.2_amd64.deb && \
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/libcudnn8-dev_8.9.7.29-1+cuda12.2_amd64.deb && \
    dpkg -i libcudnn8_8.9.7.29-1+cuda12.2_amd64.deb libcudnn8-dev_8.9.7.29-1+cuda12.2_amd64.deb && \
    rm libcudnn8_8.9.7.29-1+cuda12.2_amd64.deb libcudnn8-dev_8.9.7.29-1+cuda12.2_amd64.deb
RUN DEBIAN_FRONTEND=noninteractive apt-get update -y && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    # CUDA headers need it for interop
    libgl-dev libegl-dev libvdpau-dev \
    nvidia-utils-${CUDA_DRIVER} \
    cuda-cudart-dev-${CUDA_PKG_VERSION} \
    cuda-nvml-dev-${CUDA_PKG_VERSION} \
    cuda-cudart-${CUDA_PKG_VERSION} \
    cuda-profiler-api-${CUDA_PKG_VERSION} \
    cuda-nvcc-${CUDA_PKG_VERSION} \
    libcudnn8-dev \
    cudnn9-cuda-${CUDA_PKG_VERSION} \
    libcufft-dev-${CUDA_PKG_VERSION} \
    libcublas-dev-${CUDA_PKG_VERSION} \
    libcusparse-dev-${CUDA_PKG_VERSION}

ARG ROCM_VERSION=6.4
RUN mkdir --parents --mode=0755 /etc/apt/keyrings && \
    wget https://repo.radeon.com/rocm/rocm.gpg.key -O - | \
    gpg --dearmor | tee /etc/apt/keyrings/rocm.gpg > /dev/null && \
    echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/rocm.gpg] https://repo.radeon.com/rocm/apt/${ROCM_VERSION} noble main" > /etc/apt/sources.list.d/rocm.list && \
    echo 'Package: *\nPin: release o=repo.radeon.com\nPin-Priority: 600' > /etc/apt/preferences.d/rocm-pin-600 && \
    DEBIAN_FRONTEND=noninteractive apt update -y && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    rocminfo \
    rocm-gdb \
    rocm-smi-lib \
    rocm-llvm-dev \
    hip-runtime-amd \
    hip-dev && \
    echo '/opt/rocm/lib' > /etc/ld.so.conf.d/rocm.conf && \
    ldconfig

ENV PATH=$PATH:/opt/rocm-${ROCM_VERSION}/bin

