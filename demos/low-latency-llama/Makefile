# Compiler
NVCC?=nvcc

ifndef GPU
GPU=H100
endif

TARGET=mk_llama
SRC=llama.cu

# Python version configuration
ifndef PYTHON_VERSION
PYTHON_VERSION=3.12
endif
PYTHON3_CONFIG=/home/jeromeku/.local/share/uv/python/cpython-3.12.8-linux-x86_64-gnu/bin/python3.12-config 
NVCCFLAGS=-DNDEBUG -Xcompiler=-fPIE --expt-extended-lambda --expt-relaxed-constexpr -Xcompiler=-Wno-psabi -Xcompiler=-fno-strict-aliasing --use_fast_math -forward-unknown-to-host-compiler -O3 -Xnvlink=--verbose -Xptxas=--verbose -Xptxas=--warn-on-spills -std=c++20 -x cu -lrt -lpthread -ldl -lcuda -lcudadevrt -lcudart_static -lcublas -lineinfo
NVCCFLAGS+= -I${THUNDERKITTENS_ROOT}/include -I${MEGAKERNELS_ROOT}/include $(shell python -m pybind11 --includes) $(shell ${PYTHON3_CONFIG} --ldflags) -shared -fPIC -lpython${PYTHON_VERSION}

# nvcc llama.cu -DNDEBUG -Xcompiler=-fPIE --expt-extended-lambda --expt-relaxed-constexpr -Xcompiler=-Wno-psabi -Xcompiler=-fno-strict-aliasing 
# --use_fast_math -forward-unknown-to-host-compiler -O3 -Xnvlink=--verbose -Xptxas=--verbose -Xptxas=--warn-on-spills -std=c++20 -x cu -lrt -lpthread -ldl -lcuda -lcudadevrt -lcudart_static -lcublas -lineinfo 
#-I/home/jeromeku/kernels/megakernels/ThunderKittens/include -I/home/jeromeku/kernels/megakernels/include -I/home/jeromeku/.local/share/uv/python/cpython-3.12.8-linux-x86_64-gnu/include/python3.12 -I/home/jeromeku/kernels/megakernels/.venv/lib/python3.12/site-packages/pybind11/include 
# -L/home/jeromeku/.local/share/uv/python/cpython-3.12.8-linux-x86_64-gnu/lib  -lpthread -ldl  -lutil -lm  -shared -fPIC -lpython3.12 -DKITTENS_HOPPER -arch=sm_90a -o mk_llama.cpython-311-x86_64-linux-gnu.so

# Conditional setup based on the target GPU
ifeq ($(GPU),4090)
NVCCFLAGS+= -DKITTENS_4090 -arch=sm_89
else ifeq ($(GPU),A100)
NVCCFLAGS+= -DKITTENS_A100 -arch=sm_80
else ifeq ($(GPU),H100)
NVCCFLAGS+= -DKITTENS_HOPPER -arch=sm_90a
else
NVCCFLAGS+= -DKITTENS_HOPPER -DKITTENS_BLACKWELL -arch=sm_100a
endif

# Default target
all: $(TARGET)

# run: $(TARGET)
# 	python test.py

$(TARGET): $(SRC)
	$(NVCC) $(SRC) $(NVCCFLAGS) -o $(TARGET)$(shell ${PYTHON3_CONFIG} --extension-suffix)

# Clean target
clean:
	rm -f $(TARGET)$(shell ${PYTHON3_CONFIG} --extension-suffix)
