
## 编译TensorRT custom plugin

### 1. 编译cutom plugin.so
```shell
# git clone this repo
cd TensorRT/
git submodule update --init --recursive
export TRT_SOURCE=`pwd`
cd $TRT_SOURCE
mkdir -p build && cd build
cmake .. -DGPU_ARCHS=86 -DTRT_LIB_DIR=/usr/lib/x86_64-linux-gnu -DTRT_OUT_DIR=`pwd`/out
make nvinfer_plugin -j$(nproc)
```

### 2. 替代原有的libnvinfer_plugin.so
```shell
# After building ends successfully, libnvinfer_plugin.so* will be generated under pwd/out/ or ./build.
# backup original libnvinfer_plugin.so.x.y, e.g. libnvinfer_plugin.so.8.5.2
sudo mv /usr/lib/x86_64-linux-gnu/libnvinfer_plugin.so.8.p.q ${HOME}/libnvinfer_plugin.so.8.p.q.bak
# only replace the real file, don't touch the link files, e.g. libnvinfer_plugin.so, libnvinfer_plugin.so.8
sudo cp $TRT_SOURCE/`pwd`/out/libnvinfer_plugin.so.8.m.n  /usr/lib/x86_64-linux-gnu/libnvinfer_plugin.so.8.p.q
sudo ldconfig
```


##  设置环境变量
```shell
# docker image env
# ENV TRT_LIBPATH /usr/lib/x86_64-linux-gnu
# ENV TRT_OSSPATH /workspace/TensorRT
# ENV LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${TRT_OSSPATH}/build/out:${TRT_LIBPATH}"

# write to ~/.bashrc or /etc/profile and source it
export TRT_LIBPATH=/usr/lib/x86_64-linux-gnu
export TRT_OSSPATH=/opt/nvidia/deepstream/deepstream-6.2/workspace/TensorRT
export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:${TRT_OSSPATH}/build/out:${TRT_LIBPATH}
```

## export trt engine with custom plugins
```shell
trtexec --onnx=yolov8n-seg-nms.onnx --saveEngine=yolov8-seg-nms.trt   --workspace=8192 --fp16 --minShapes=images:1x3x640x640 --optShapes=images:4x3x640x640 --maxShapes=images:8x3x640x640 --shapes=images:4x3x640x640 --plugins 


trtexec --onnx=yolov8n-seg-nms.onnx --saveEngine=yolov8-seg-nms.trt   --workspace=8192 --fp16  --plugins 

```


## 参考
- [TensorRT custom plugin](https://github.com/hiennguyen9874/TensorRT)