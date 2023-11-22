<!--Copyright 2023 The HuggingFace Team. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with
the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on
an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the
specific language governing permissions and limitations under the License.
-->

# Interrupt the Diffusion Process

The following Pipelines support interrupting the diffusion process via callback

- [StableDiffusionPipeline](../api/pipelines/stable_diffusion/overview.md)
- [StableDiffusionImg2ImgPipeline](..api/pipelines/stable_diffusion/img2img.md)
- [StableDiffusionInpaintPipeline](..api/pipelines/stable_diffusion/inpaint.md)
- [StableDiffusionXLPipeline](../api/pipelines/stable_diffusion/stable_diffusion_xl.md)
- [StableDiffusionXLImg2ImgPipeline](../api/pipelines/stable_diffusion/stable_diffusion_xl.md)
- [StableDiffusionXLInpaintPipeline](../api/pipelines/stable_diffusion/stable_diffusion_xl.md)

This is particularly useful when building UIs that work with Diffusers as it allows users to stop the generation process if they are unhappy with the intermediate results.

Callback functions take the following arguments: `pipe`, `i`, `t`, `callback_kwargs` and must return `callback_kwargs`. Interrupt the diffusion process by setting `pipe._interrupt = True` inside the callback function. You are free to implement custom stopping logic inside the callback.

In the example below, we stop the diffusion process after 10 steps.

```python
from diffusers import StableDiffusionPipeline

pipe = StableDiffusionPipeline.from_pretrained("runwayml/stable-diffusion-v1-5")
pipe.enable_model_cpu_offload()
num_inference_steps = 50

def interrupt_callback(pipe, i, t, callback_kwargs):
    stop_idx = 10
    if i == stop_idx:
        pipe._interrupt = True

    return callback_kwargs

pipe(
    "A photo of a cat",
    num_inference_steps=num_inference_steps,
    callback_on_step_end=interrupt_callback,
)
```