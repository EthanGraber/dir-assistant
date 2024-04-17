# dir-assistant

Chat with your current directory's files using a local LLM.

![Demo of dir-assistant being run](demo.gif)

This project runs LLMs via the fantastic [llama-cpp-python](https://github.com/abetlen/llama-cpp-python) package.

Dir-assistant has platform support for CPU (OpenBLAS), Cuda, ROCm, Metal, OpenCL, Vulkan, Kompute, and SYCL.

## Setup

Install `pyenv` if you have not already: https://github.com/pyenv/pyenv-installer

Clone this repo then run:
```
./setup.sh
```

Note: llama-cpp-python's wheel compile was quite buggy for me on both AMD and Nvidia systems I tested.
You may need to manually change the llama-cpp-python wheel compile options to make it build successfully. 
To do this, activate the virtualenv (`pyenv activate dir-assistant`) and then run `pip install llama-cpp-python`
with the options. Reference the install instructions here for more info: https://github.com/abetlen/llama-cpp-python

Here is my working formula for a 7900XT on ROCM 6.0.2:
```
pyenv activate dir-assistant
CC=/opt/rocm/llvm/bin/clang CXX=/opt/rocm/llvm/bin/clang++ CMAKE_ARGS="-DLLAMA_HIPBLAS=on -DCMAKE_BUILD_TYPE=Release -DLLAMA_HIPBLAS=ON -DLLAMA_CUDA_DMMV_X=64 -DLLAMA_CUDA_MMV_Y=4 -DCMAKE_PREFIX_PATH=/opt/rocm -DAMDGPU_TARGETS=gfx1100" pip install --no-cache-dir llama-cpp-python
```

## Model Download

Download your favorite LLM gguf and place it in the models directory.

## Configure

This script should be run once when you first install `dir-assistant`, and then again whenever you would
like to modify its configuration:

```
./config.sh
```

Alternatively you can edit `config.json` once it is created.

## Run

First export the necessary environment variables at the start of every terminal session
(this script can be added to your `.bashrc`):

```
soruce /path-to/dir-assistant/export.sh
```

Then navigate to the directory you would like your LLM to see and run dir-assistant:

```
dir-assistant
```

If you'd like to ignore some files or directories, you can list them with the `--ignore` argument:

```
dir-assistant --ignore some-project-directory .git .gitignore
```

There's also a global ignore list in `config.json`.

## Limitations

- This project uses a naive approach for including files from the current working directory. It simply pastes them into the system instructions. For this reason, dir-assistant will only work well with projects that can be contained in your LLM's context window.
- Dir-assistant only detects and reads text files at this time.
- This is a personal project that works for my needs but might not work for yours. If you make any adjustments so it works for you, I'd appreciate it if you made a PR.

## Recommended Models

- [CodeQwen 1.5 7B Chat](https://huggingface.co/Qwen/CodeQwen1.5-7B-Chat-GGUF): This is an excellent coding model with an extremely accurate 64K token context window. It should work well with projects under 5k lines of code.