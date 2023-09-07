## Docker

Docker 本身并没有定义或使用自己的`系统调用`。Docker 是一个容器化平台，它使用现有的操作系统功能来创建和管理容器。以下是一些与 Docker 相关的关键技术：

* **Linux Namespaces**：Docker 在 Linux 上使用 namespaces 来隔离容器的进程、网络、用户和文件系统。这确保了容器之间的隔离和安全。

* **Control Groups (cgroups)**：Docker 使用 cgroups 来限制和监控容器对系统资源（如 CPU、内存、磁盘 I/O 等）的使用。

* **Overlay Filesystems**：Docker 使用叠加文件系统（如 Overlay2）来实现容器的文件系统。这允许容器共享一些读取的文件层，同时保持对写入操作的隔离。

* **系统调用**：Docker 容器内的应用程序通过主机操作系统的正常系统调用与内核交互。这些系统调用由主机操作系统的内核处理，就像它们是在主机上直接运行的普通进程一样。

* **安全限制**：Docker 可以使用如 AppArmor 或 SELinux 等安全模块来限制容器可以执行的系统调用，增强安全性。

总的来说，Docker 利用了现有的操作系统技术和内核功能来实现容器化，而不是通过定义自己的系统调用。这使得 Docker 可以在不修改内核的情况下工作，并确保了与主机操作系统的兼容性和互操作性。


## Docker镜像标签

Docker 镜像的标签通常由以下几部分构成：

**Registry**: 这是 Docker 镜像存储的位置，通常是一个 URL。例如，对于 Docker Hub，这部分通常会被省略，因为 Docker 默认会使用 Docker Hub。如果您使用的是其他的 Docker Registry（例如 Google Container Registry 或者您自己搭建的私有 Registry），这部分就需要明确指出。例如，gcr.io 或 192.168.1.100:5000。

**Username or Project ID**: 这部分通常是 Docker Hub 的用户名或者其他 Docker Registry 的项目 ID。例如，在 library/nginx 中，library 就是这部分。

**Repository Name**: 这是 Docker 镜像的名称。例如，在 library/nginx 中，nginx 就是这部分。

**Tag**: 这部分用于标识 Docker 镜像的版本。如果没有明确指定，这部分默认为 latest。

这些部分通过 / 和 : 连接在一起，形成了 Docker 镜像的完整标签。例如，gcr.io/my-project/my-image:1.0 或 library/nginx:latest。

## Docker基本使用

#### 构建

```shell
docker build . -f ./examples/ex-1/Dockerfile -t rkrispin/vscode-python:ex1
```
在当前目录（即 .）下，使用 ./examples/ex-1/Dockerfile 作为 Dockerfile，构建一个新的 Docker 镜像，镜像的名称是 rkrispin/vscode-python，标签是 ex1

#### 运行

```shell
docker run -it --rm rkrispin/vscode-python:ex1 bash
```
运行一个新的 Docker 容器，这个容器使用 rkrispin/vscode-python:ex1 镜像，当容器启动时，运行 bash 命令。容器运行在交互模式下，有一个伪终端，所以你可以在容器内输入命令并看到输出。当容器退出时，它会自动被删除

#### 取出文件（例如.whl）

```shell
# 从镜像内取
docker cp xgboost-builder:/tmp/xgboost/python-package/dist/*.whl/ .
# 挂载宿主机目录
docker run -it --rm -v /Users/dxm/Desktop/docker-python/centos7:/output xgboost-builder bash
```

#### Dockerfile

**RUN**: 在 Dockerfile 中，RUN 指令是在镜像*构建*阶段执行的，它用于在镜像中安装软件、设置环境变量、编译代码等操作。一旦 Docker 镜像构建完成，RUN 指令定义的所有操作都已经在镜像中完成，RUN执行的结果（例如安装的软件、设置的环境变量等）会被保存在 Docker 镜像中。在 Dockerfile 中的每个 RUN 指令都会启动一个新的*shell进程*。

**ENTRYPOINT** 和 **CMD**: ENTRYPOINT 和 CMD 指令在 Docker 容器*启动*时执行（在构建镜像时不会执行）。ENTRYPOINT 提供了容器的默认执行命令（前台进程），CMD 则提供了默认的参数（CMD在ENTRYPOINT未定义时也可视为命令）。docker run 命令则在运行容器时可以覆盖这些默认行为，例如`docker run --entrypoint python myimage script2.py` 会同时覆盖ENTRYPOINT 和 CMD 指令

```shell
# Dockerfile: Make entrypoint.sh executable and set it as the entrypoint
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

#!/bin/bash
source /opt/conda/etc/profile.d/conda.sh
conda activate $ENV_NAME
exec "$@"

docker run myimage python script.py
```
当你运行 `docker run myimage python script.py` 这个命令时，Docker会进入 {ENV_NAME} 环境并执行 `python script.py`，这使得环境变量设置保持在同一个shell进程生命周期


## 在联网的linux/arm64/v8架构机器构建linux/amd64架构镜像

同一硬件架构可以运行多种不同的操作系统，这就是 Docker 镜像跨平台的基础。因为 Docker 镜像包含了运行应用所需的所有依赖和环境，这使得应用可以在任何安装了 Docker 的系统上一致地运行。

以下是一些常见的硬件架构以及软件系统

x86-64（也称为 amd64）：x86 架构的 64 位版本，它支持更大的内存和更多的寄存器。这种架构最初由 AMD 开发，因此也被称为 amd64。它可以运行多种操作系统，包括各种版本的 Windows（如 Windows 7、Windows 10、Windows Server 等）、Linux（如 Ubuntu、CentOS、Debian 等）、macOS（在 2020 年之前的版本），以及其他如 FreeBSD、NetBSD 等

ARM64（也称为 arm64/v8）：ARM 架构的 64 位版本，它提供了更高的性能和更大的内存支持。ARM64 架构可以运行多种操作系统，包括 Android、iOS、Linux、Windows 10（ARM 版本）和 macOS（在 Apple 的 M1 芯片上）

所以当我们说 Docker 镜像是跨平台的，我们是指在同一硬件架构的不同操作系统之间。而跨不同硬件架构需要构建针对特定硬件架构的镜像。这就是为什么 Docker 提供了 multi-architecture builds 功能，它允许您为多种硬件架构构建镜像

*base镜像*: 这是一个最小化的CUDA基础镜像，只包含CUDA运行时库和必要的系统工具和库。
它不包含编译CUDA代码所需的开发工具，如nvcc（NVIDIA CUDA编译器）。
适用于运行已经编译好的CUDA应用程序，不需要进行任何CUDA开发或编译的场景。

*devel镜像*: 包含了base镜像中的所有内容，此外还添加了CUDA开发工具，包括头文件、静态库以及nvcc。
这个镜像允许您在容器内编译和开发CUDA应用程序。
由于包含了额外的开发工具，所以这个镜像的大小通常比base镜像要大

```shell
docker build --no-cache --platform linux/amd64 -t yanwubin/bmodel:v1.0 .
docker save yanwubin/bmodel:v1.0|gzip > bmodel_v1.0.tar.gz
docker pull --platform linux/amd64 yanwubin/bmodel:v1.0
```

## Dockerfile:COPY  devcontainer:"context"

**context**: 在 devcontainer.json 文件中，"context" 属性指定了 Docker 构建上下文的路径。Docker 构建上下文是一个文件和目录的集合，它们在构建 Docker 镜像时被发送到 Docker daemon。在 Dockerfile 中，你可以引用这些文件和目录，例如，使用 COPY 指令将它们添加到 Docker 镜像中。"context" 属性的值通常是一个相对于 devcontainer.json 文件的路径，或者是一个绝对路径

**COPY**: 在 Dockerfile 中，COPY 指令用于将文件和目录从 Docker 构建上下文复制到 Docker镜像中。COPY 指令的源路径必须是在 Docker 构建上下文中的一个路径，目标路径是在 Docker 镜像中的一个路径。例如，COPY . /app 会将 Docker 构建上下文中的所有文件和目录复制到 Docker 镜像的 /app 目录中

## Dockerfile:ENV ARG devcontainer:"remoteEnv" "runArgs" ${localEnv:MY_VAR}

**ARG**: ARG 可以在构建Docker镜像时通过 --build-arg 选项来设置, 只在构建 Docker 镜像时可用

**ENV**: 在 Dockerfile 中，ENV 指令用于设置环境变量。这些环境变量在**构建** Docker 镜像时被设置，并且在运行该 Docker 镜像的任何容器中都可用 

**remoteEnv**: 在 devcontainer.json 文件中，remoteEnv 属性用于设置在开发容器中的环境变量。这些环境变量在开发容器*启动*时被设置，并且在容器中的任何位置可用

然而，这些环境变量都是硬编码在 devcontainer.json 文件中的，如果你想要在多个 devcontainer.json 文件中共享环境变量，或者你不希望将某些敏感的环境变量（例如，API 密钥或者密码）存储在 devcontainer.json 文件中，你可以使用devcontainer.env 文件

**runArgs**: ["--env-file",".devcontainer/devcontainer.env"] devcontainer.env 文件是一个简单的文本文件，它包含了 KEY=VALUE 格式的行。这些环境变量在 Docker 容器启动时被设置，可以在容器中的任何位置使用

总的来说，ENV 和 remoteEnv/runArgs 都是用于设置环境变量的，但是它们的用法和范围是不同的。ENV 在 Dockerfile 中使用，设置的环境变量在运行该 Docker 镜像的任何容器中都可用。remoteEnv/runArgs 在 devcontainer.json 文件中使用，设置的环境变量只在当前的开发容器中可用

**localEnv**: 需要注意的是，这种方式只能获取到在启动VS Code时已经存在的环境变量的值




