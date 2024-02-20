## 

## Test Environment

This repository includes the pre-test for the LFX Mentorship (Mar-May, 2024) Proposal #3170: Integrate whisper.cpp as a new WASI-NN backend. You can find more details here:

here is my computer config.

![](/Users/heng/Downloads/pre-test/pic/2024-02-20-15-16-51-image.png)

## Section one - Whisper.cpp

### Building steps

first install the necessary libraries

> brew install openblas

> brew install sdl2

clone the whisper.cpp repository and build it

> git clone https://github.com/ggerganov/whisper.cpp.git and cd whisper.cpp

downland the base whisper model

> ./models/download-ggml-model.sh base.en

next, build the whisper.cpp, Openblas can supports the  accelerate of inference for whisper.cpp

> WHISPER_OPENBLAS=1 make steam -j4



### Real-time audio input example

> ./stream -m ./models/ggml-base.en.bin -t 8 --step 500 --length 5000

![](/Users/heng/Downloads/pre-test/pic/2024-02-20-16-39-57-image.png)

## Section 2 - WasmEdge

### Preparation

#### Install software dependencies

> brew install ninja

#### Clone the remote repository

> git clone https://github.com/WasmEdge/WasmEdge.git && git checkout hydai/0.13.5_ggml_lts

### Build WasmEdge with WASI-NN llama.cpp Backend:

With the help of this guide, I  build WasmEdge with WASI-NN llama.cpp backend.

>  cmake -GNinja -Bbuild -DCMAKE_BUILD_TYPE=Release \  
>   -DWASMEDGE_PLUGIN_WASI_NN_BACKEND="GGML" \  
>   -DWASMEDGE_PLUGIN_WASI_NN_GGML_LLAMA_METAL=OFF\  
>   -DWASMEDGE_PLUGIN_WASI_NN_GGML_LLAMA_BLAS=ON\  
> 
> .

![](/Users/heng/Downloads/pre-test/pic/2024-02-20-16-38-48-image.png)

build and install

> cmake --build build
>  cmake –install build

### Downland the model

- get the pre-built WASM application llama/wasmedge-ggml-llama.wasm, provided by this [repository](https://github.com/second-state/WasmEdge-WASINN-examples/blob/master/wasmedge-ggml/README.md).

- 1. downloaded the llama model:  `$ curl -LO https://huggingface.co/TheBloke/Llama-2-7b-Chat-GGUF/resolve/main/llama-2-7b-chat.Q5_K_M.gguf`

### Execute the WASM

> wasmedge --dir .:. \
>   --nn-preload default:GGML:AUTO:llama-2-7b-chat.Q5_K_M.gguf \
>   wasmedge-ggml-llama.wasm default

![](/Users/heng/Downloads/pre-test/pic/2024-02-20-16-46-36-image.png)


