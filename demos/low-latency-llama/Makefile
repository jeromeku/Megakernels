# Compiler
NVCC=/home/jeromeku/cuda-toolkit/bin/nvcc
GPU=H100
TARGET=mk_llama
SRC=llama.cu
PYTHON_VERSION=3.12
MEGAKERNELS_ROOT=/home/jeromeku/megakernels
THUNDERKITTENS_ROOT=${MEGAKERNELS_ROOT}/ThunderKittens
PYTHON_LD_FLAGS=-L/home/jeromeku/.local/share/uv/python/cpython-3.12.11-linux-x86_64-gnu/lib  -lpthread -ldl  -lutil -lm 

TARGET=mk_llama
SRC=llama.cu
#-DNDEBUG --use_fast_math -O3
_NVCCFLAGS=-Xcompiler=-fPIE --expt-extended-lambda --expt-relaxed-constexpr -Xcompiler=-Wno-psabi -Xcompiler=-fno-strict-aliasing -forward-unknown-to-host-compiler \
 -Xnvlink=--verbose \
-Xptxas=--verbose -Xptxas=--warn-on-spills \
-std=c++20 -x cu -lrt -lpthread -ldl -lcuda -lcudadevrt -lcudart_static -lcublas\
-lineinfo

_NVCCFLAGS+= -I${THUNDERKITTENS_ROOT}/include -I${MEGAKERNELS_ROOT}/include $(shell python3 -m pybind11 --includes) ${PYTHON_LD_FLAGS} -shared -fPIC -lpython${PYTHON_VERSION}


# Conditional setup based on the target GPU
ifeq ($(GPU),4090)
_NVCCFLAGS+= -DKITTENS_4090 -arch=sm_89
else ifeq ($(GPU),A100)
_NVCCFLAGS+= -DKITTENS_A100 -arch=sm_80
else ifeq ($(GPU),H100)
_NVCCFLAGS+= -DKITTENS_HOPPER -arch=sm_90a
else
_NVCCFLAGS+= -DKITTENS_HOPPER -DKITTENS_BLACKWELL -arch=sm_100a
endif

NVCCFLAGS_DEBUG=${_NVCCFLAGS} -O0 -DMK_DEBUG -g
NVCCFLAGS=${_NVCCFLAGS} -O3 --use_fast_math -DNDEBUG

DEBUG_TARGET=${TARGET}.debug

# Default target
all: $(DEBUG_TARGET)

# run: $(TARGET)
# 	python test.py
EXTENSION_SUFFIX=.cpython-312-x86_64-linux-gnu.so
EXTENSION_NAME=${TARGET}${EXTENSION_SUFFIX}
EXTENSION_NAME_DEBUG=${TARGET}.debug${EXTENSION_SUFFIX}

$(DEBUG_TARGET): $(SRC)
	$(NVCC) $(SRC) $(NVCCFLAGS) -o ${EXTENSION_NAME_DEBUG}

$(TARGET): $(SRC)
	$(NVCC) $(SRC) $(NVCCFLAGS) -o ${EXTENSION_NAME}

# Clean target
clean:
	rm -f ${EXTENSION_NAME} ${EXTENSION_NAME_DEBUG}
