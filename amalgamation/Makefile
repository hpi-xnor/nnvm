export NNVM_ROOT=`pwd`/..

export CFLAGS = -std=c++11 -Wall -O3 -Wno-unknown-pragmas -funroll-loops -Iinclude -fPIC -DDMLC_LOG_STACK_TRACE=0

ifeq ($(OPENMP), 1)
	CFLAGS += -fopenmp
	LDFLAGS += -fopenmp
endif

ifeq ($(ANDROID), 1)
	CFLAGS += -I${NDK_TOOLCHAIN_ROOT}/include/c++/4.9.x

	ifeq ($(CC), arm-linux-androideabi-clang)
		LDFLAGS += -L${NDK_TOOLCHAIN_ROOT}/arm-linux-androideabi/lib/armv7-a
	endif

	ifeq ($(CC), arm-linux-androideabi-gcc)
		CFLAGS +=-isystem ${NDK_TOOLCHAIN_ROOT}/include/c++/4.9.x
	endif
endif


.PHONY: all clean

all: libnnvm.a

nnvm.cc:
	python generate.py $@

nnvm.d: nnvm.cc
	${CXX} ${CFLAGS} -MD -MF $@ \
		-I ${NNVM_ROOT}/ -I ${NNVM_ROOT}/include \
		-D__MIN__=$(MIN) -c $+
	rm nnvm.o

nnvm-all.cc: nnvm.d nnvm.cc
	python ./amalgamation.py $+ $@

nnvm-all.o: nnvm-all.cc
	${CXX} ${CFLAGS} -fPIC -o $@ -c $+

libnnvm.a: nnvm-all.o
	ar rcs $@ $+

clean:
	rm -f *.d *.o *.so *.a nnvm-all.cc nnvm.cc
