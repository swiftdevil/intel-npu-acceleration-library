# Basic usage

For implemented examples, please check the `examples` folder

## Run a single MatMul in the NPU

```python
from intel_npu_acceleration_library.backend import MatMul
import numpy as np

inC, outC, batch = ... # Define your own values

# Create both inputs
X1 = np.random.uniform(-1, 1, (batch, inC)).astype(np.float16)
X2 = np.random.uniform(-1, 1, (outC, inC)).astype(np.float16)

mm = MatMul(inC, outC, batch, profile=False)

result = mm.run(X1, X2)

```

## Compile a model for the NPU

If you have `pytorch`>=2.0.0 installed you can use torch compile to optimize your model for the NPU

```python
import intel_npu_acceleration_library
import torch

# Compile model for the NPU
# model a torch.nn.Module class. Model can be quantized JIT
optimized_model = torch.compile(model, backend="npu")

# Use the model as usual

```

In windows torch.compile is not supported yet. So you might want to use the explicit function `intel_npu_acceleration_library.compile`. This is true also if you use a `pytorch` version < 2.0.0

To do this, you just need to call the `compile` function with your model and the compiler configuration `CompilerConfig` to compile and optimize the model for the NPU.
```python
import intel_npu_acceleration_library
from intel_npu_acceleration_library.compiler import CompilerConfig
compiler_conf = CompilerConfig(dtype=torch.int8)
optimized_model = intel_npu_acceleration_library.compile(model, compiler_conf)

# Use the model as usual

```

To compile and optimize a single layer of a model to be pushed to the NPU as one block, you can set `use_to=True` in the the compiler configuration `CompilerConfig`.
```python
import intel_npu_acceleration_library
from intel_npu_acceleration_library.compiler import CompilerConfig
compiler_conf = CompilerConfig(use_to=True, dtype=torch.int8)
optimized_block = intel_npu_acceleration_library.compile(single_block, compiler_conf)

```

## Training (**Experimental!**)

It is possible to use Intel® NPU Acceleration Library to train a model. As before you just need to call the `compile` function, this time with `training=True`. This allows to use the same training script you use in other device with a very minimal modifications.

```python
import intel_npu_acceleration_library
from intel_npu_acceleration_library.compiler import CompilerConfig
compiler_conf = CompilerConfig(dtype=torch.float32, training=True)
compiled_model = intel_npu_acceleration_library.compile(model, compiler_conf)
```
