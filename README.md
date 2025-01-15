# 备注

## 前言
pull 该仓库是为了找到好的在CPU上执行的稀疏算子。但是经过调研发现他并没有将这部分开源出来。于是，我就把目前调研的相关东西标注出来。

## 记录
1. 调用 onnx 执行相关操作主要靠 Engine 类。
2. 在 Engine 类中，通过调用 `deepsparse.lib` 中的 `compile_model_and_engine` 函数来编译模型和引擎。
3. 在 `compile_model_and_engine` 函数中，通过调用 `deepsparse.lib` 中的 `compile_model` 函数来编译模型。
4. 在 `compile_model` 函数中，通过调用 `deepsparse.lib` 中的 `compile_onnx` 函数来编译 onnx 模型。
5. 初始化 engine库在:
```py
def init_deepsparse_lib():
    try:
        onnxruntime_neuralmagic_so_path = os.path.join(
            get_neuralmagic_binaries_dir(), "deepsparse_engine.so"
        )
        spec = importlib.util.spec_from_file_location(
            "deepsparse.{}.deepsparse_engine".format(AVX_TYPE),
            onnxruntime_neuralmagic_so_path,
        )
        engine = importlib.util.module_from_spec(spec)
        spec.loader.exec_module(engine)

        return engine
    except ImportError:
        raise ImportError(
            "Unable to import deepsparse engine binaries. "
            "Please contact support@neuralmagic.com"
        )

```
中,他会从某个地址狭下载 .so 可执行文件。