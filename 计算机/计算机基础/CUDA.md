# CUDA 编程完全指南：GPU 并行计算从入门到精通

---

## 一、CUDA 核心概念与架构

### 1. GPU 与 CPU 计算范式对比
| **维度**        | CPU                  | GPU                  |
|-----------------|----------------------|----------------------|
| 核心数量         | 4-64（多核）          | 数千（流处理器）       |
| 线程模型         | 重量级线程（上下文切换高开销）| 轻量级线程（纳秒级切换）|
| 适用场景         | 复杂逻辑、低并行度任务 | 高并行数据计算任务      |
| 内存带宽         | 50-100 GB/s          | 400-1000 GB/s (NVIDIA A100) |

### 2. CUDA 核心架构组件
- **SM（Streaming Multiprocessor）**：GPU核心执行单元，包含：
  - CUDA Core（浮点运算单元）
  - Tensor Core（矩阵加速，支持混合精度）
  - RT Core（光线追踪专用）
- **内存层次**：
  - 全局内存（Global Memory）：显存，带宽高但延迟高
  - 共享内存（Shared Memory）：片上缓存，块内线程共享
  - 寄存器（Register）：每个线程私有，零延迟访问
  - 常量/纹理内存：优化特定访问模式

---

## 二、CUDA 编程模型详解

### 1. 线程层次结构
| **层级**        | 描述                   | 访问范围             |
|-----------------|------------------------|---------------------|
| Thread          | 最小执行单元            | 私有寄存器/本地变量    |
| Block           | 线程集合（最多1024线程）| 共享内存、同步操作     |
| Grid            | Block 集合             | 全局内存、原子操作     |

### 2. 内核函数定义与启动
```cpp
// 向量加法内核函数
__global__ void vecAdd(float* A, float* B, float* C, int n) {
    int i = blockIdx.x * blockDim.x + threadIdx.x;
    if (i < n) C[i] = A[i] + B[i];
}

// 主机端调用
int main() {
    int N = 1<<20;  // 1百万元素
    vecAdd<<<(N+255)/256, 256>>>(d_A, d_B, d_C, N);
}
```

---

## 三、CUDA 内存管理实战

### 1. 内存操作 API
```cpp
float *d_A;
// 分配全局内存
cudaMalloc(&d_A, N * sizeof(float));  

// 主机到设备拷贝
cudaMemcpy(d_A, h_A, N * sizeof(float), cudaMemcpyHostToDevice);

// 使用共享内存
__shared__ float s_data[512];
```

### 2. 内存访问优化策略
- **合并访问**：线程连续访问全局内存（避免分散访问）
  ```cpp
  // 优化前：跨步访问
  int i = threadIdx.x + blockIdx.x * blockDim.x * 2;
  
  // 优化后：合并访问
  int i = blockIdx.x * blockDim.x + threadIdx.x;
  ```
- **Bank Conflict 避免**：共享内存分32个Bank，确保线程访问不同Bank
- **使用L2 Cache**：通过`__ldg`指令强制缓存读取

---

## 四、CUDA 高级特性与性能调优

### 1. 流与异步操作
```cpp
cudaStream_t stream1, stream2;
cudaStreamCreate(&stream1);
cudaStreamCreate(&stream2);

// 异步内存拷贝
cudaMemcpyAsync(d_A, h_A, N, cudaMemcpyHostToDevice, stream1);

// 多流并行执行
kernel1<<<grid, block, 0, stream1>>>(...);
kernel2<<<grid, block, 0, stream2>>>(...);
```

### 2. 性能分析工具
| **工具**        | 功能                      | 示例命令                     |
|-----------------|--------------------------|-----------------------------|
| nvprof          | 命令行性能分析器           | `nvprof ./myapp`            |
| Nsight Systems  | 系统级性能可视化            | `nsys profile -o report ./myapp` |
| Nsight Compute  | 内核级指令分析              | `ncu -k myKernel ./myapp`   |

---

## 五、CUDA 应用场景与库

### 1. 典型应用领域
- **深度学习**：cuDNN 加速卷积运算，TensorRT 推理优化
- **科学计算**：cuBLAS 矩阵运算，CUDA Fortran 数值模拟
- **图像处理**：NPP (NVIDIA Performance Primitives) 图像算法加速
- **金融计算**：Monte Carlo 模拟并行化

### 2. 混合编程案例
```cpp
// CUDA 加速的矩阵乘法
void matrixMul(float* A, float* B, float* C, int M, int N, int K) {
    dim3 block(16, 16);
    dim3 grid((N + block.x - 1)/block.x, (M + block.y - 1)/block.y);
    matrixMulKernel<<<grid, block>>>(A, B, C, M, N, K);
}
```

---

## 六、常见问题与调试技巧

### 1. 错误排查
| **错误类型**     | 解决方案                     |
|------------------|----------------------------|
| Kernel Launch Failure | 检查线程配置（block/grid尺寸）|
| Illegal Memory Access | 使用`cuda-memcheck`工具检测   |
| 内核未执行       | 添加`cudaDeviceSynchronize()`|

### 2. 最佳实践
- **统一内存（Unified Memory）**：简化内存管理
  ```cpp
  cudaMallocManaged(&data, size);  // CPU/GPU统一地址空间
  ```
- **避免线程分歧**：确保同一Warp内的线程执行相同分支
- **合理使用原子操作**：优先使用块内共享内存减少全局原子操作

---

掌握 CUDA 编程可充分释放 GPU 的并行计算潜力。建议从向量运算等基础案例入手，逐步掌握内存优化和高级特性，结合Nsight工具持续调优。 🚀🎮