#DualSPHysics4-LiquidGas GPU/CPU v1.010 (v4.0.048) (02-03-2020)

#=============== Compilation Options ===============
USE_GCC5=NO
USE_DEBUG=NO
USE_FAST_MATH=YES
USE_NATIVE_CPU_OPTIMIZATIONS=NO
COMPILE_FMTFILES=YES
COMPILE_WAVEGEN=YES

LIBS_DIRECTORIES=-L./
LIBS_DIRECTORIES:=$(LIBS_DIRECTORIES) -L../lib/linux_gcc

EXECNAME=DualSPHysics4.0_LiquidGas_linux64
EXECS_DIRECTORY=../../../bin/linux

ifeq ($(USE_DEBUG), YES)
  CCFLAGS=-c -O0 -g -Wall -fopenmp -D_WITHGPU
else
  CCFLAGS=-c -O3 -fopenmp -D_WITHGPU
  ifeq ($(USE_FAST_MATH), YES)
    CCFLAGS+= -ffast-math
  endif
  ifeq ($(USE_NATIVE_CPU_OPTIMIZATIONS), YES)
    CCFLAGS+= -march=native
  endif
endif
CC=g++
CCLINKFLAGS=-fopenmp -lgomp

#Required for GCC versions >=5.0
ifeq ($(USE_GCC5), YES)
  CCFLAGS+=-D_GLIBCXX_USE_CXX11_ABI=0
  CCLINKFLAGS+=-D_GLIBCXX_USE_CXX11_ABI=0
  NCCFLAGS+=-D_GLIBCXX_USE_CXX11_ABI=0
endif

ifeq ($(COMPILE_FMTFILES), NO)
  CCFLAGS:=$(CCFLAGS) -DDISABLE_FMTFILES
endif
ifeq ($(COMPILE_WAVEGEN), NO)
  CCFLAGS:=$(CCFLAGS) -DDISABLE_WAVEGEN
endif

#=============== CUDA selection ===============
CUDAVER=92

#=============== CUDA toolkit directory (make appropriate for local CUDA installation) ===============
ifeq ($(CUDAVER),00)
  DIRTOOLKIT=/usr/local/cuda
endif
ifeq ($(CUDAVER),75)
  DIRTOOLKIT=/exports/opt/NVIDIA/cuda-7.5
endif
ifeq ($(CUDAVER),91)
  DIRTOOLKIT=/exports/opt/NVIDIA/cuda-9.1
endif
ifeq ($(CUDAVER),92)
  DIRTOOLKIT=/exports/opt/NVIDIA/cuda-9.2
endif

#=============== Select GPU architectures ===============
ifeq ($(CUDAVER),00)
  GENCODE:=$(GENCODE) -gencode=arch=compute_20,code=\"sm_20,compute_20\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_30,code=\"sm_30,compute_30\"
endif
ifeq ($(CUDAVER),75)
  GENCODE:=$(GENCODE) -gencode=arch=compute_20,code=\"sm_20,compute_20\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_30,code=\"sm_30,compute_30\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_35,code=\"sm_35,compute_35\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_37,code=\"sm_37,compute_37\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_50,code=\"sm_50,compute_50\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_52,code=\"sm_52,compute_52\"
endif
ifeq ($(CUDAVER),91)
  GENCODE:=$(GENCODE) -gencode=arch=compute_30,code=\"sm_30,compute_30\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_35,code=\"sm_35,compute_35\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_50,code=\"sm_50,compute_50\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_52,code=\"sm_52,compute_52\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_61,code=\"sm_61,compute_61\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_70,code=\"sm_70,compute_70\"
endif
ifeq ($(CUDAVER),92)
  # module load cuda/9.2
  GENCODE:=$(GENCODE) -gencode=arch=compute_30,code=\"sm_30,compute_30\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_35,code=\"sm_35,compute_35\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_50,code=\"sm_50,compute_50\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_52,code=\"sm_52,compute_52\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_61,code=\"sm_61,compute_61\"
  GENCODE:=$(GENCODE) -gencode=arch=compute_70,code=\"sm_70,compute_70\"
endif


#=============== Files to compile ===============
OBJXML=JXml.o tinystr.o tinyxml.o tinyxmlerror.o tinyxmlparser.o
OBJSPHMOTION=JMotion.o JMotionMov.o JMotionObj.o JMotionPos.o JSphMotion.o
OBCOMMON=Functions.o FunctionsMath.o JBinaryData.o JException.o JLog2.o JMeanValues.o JObject.o JRadixSort.o JRangeFilter.o JReadDatafile.o randomc.o
OBCOMMONDSPH=JPartDataBi4.o JPartDataHead.o JPartFloatBi4.o JPartOutBi4Save.o JSpaceCtes.o JSpaceEParms.o JSpaceParts.o JSpaceProperties.o
OBSPH=JArraysCpu.o JCellDivCpu.o JCfgRun.o JPartsOut.o JSaveDt.o JSph.o JSphAccInput.o JSphCpu.o JSphDtFixed.o JSphVisco.o JTimeOut.o main.o
OBSPHSINGLE=JCellDivCpuSingle.o JPartsLoad4.o JSphCpuSingle.o
OBCOMMONGPU=JObjectGpu.o 
OBSPHGPU=JArraysGpu.o JBlockSizeAuto.o JCellDivGpu.o JSphGpu.o JPtxasInfo.o 
OBSPHSINGLEGPU=JCellDivGpuSingle.o JSphGpuSingle.o
OBCUDA=JCellDivGpu_ker.o JCellDivGpuSingle_ker.o JSphGpu_ker.o

OBJECTS=$(OBJXML) $(OBJSPHMOTION) $(OBCOMMON) $(OBCOMMONDSPH) $(OBSPH) $(OBSPHSINGLE)
OBJECTS:=$(OBJECTS) $(OBCOMMONGPU) $(OBSPHGPU) $(OBSPHSINGLEGPU) $(OBCUDA)

#=============== DualSPHysics libs to be included ===============
JLIBS=${LIBS_DIRECTORIES}
ifeq ($(COMPILE_FMTFILES), YES)
  JLIBS:=$(JLIBS) -ljformatfiles2_64
endif
ifeq ($(COMPILE_WAVEGEN), YES)
  JLIBS:=$(JLIBS) -ljwavegen_64
endif

#=============== GPU Code Compilation ===============
CCFLAGS := $(CCFLAGS) -I./ -I$(DIRTOOLKIT)/include
CCLINKFLAGS := $(CCLINKFLAGS) -L$(DIRTOOLKIT)/lib64 -lcudart_static -ldl -lrt
NCC=nvcc
ifeq ($(USE_DEBUG), NO)
  NCCFLAGS+=-c $(GENCODE) -O3 -ccbin $(CC)
else
  NCCFLAGS+=-c $(GENCODE) -O0 -ccbin $(CC) -g
endif
ifeq ($(USE_FAST_MATH), YES)
  NCCFLAGS+= -use_fast_math
endif

all:$(EXECS_DIRECTORY)/$(EXECNAME) 
	rm -rf *.o
ifeq ($(USE_DEBUG), NO)
	@echo "  --- Compiled Release GPU version ---"
else
	@echo "  --- Compiled Debug GPU version ---"
	mv $(EXECNAME) $(EXECNAME)_debug
endif

$(EXECS_DIRECTORY)/$(EXECNAME):  $(OBJECTS)
	$(CC) $(OBJECTS) $(CCLINKFLAGS) -o $@ $(JLIBS)

.cpp.o: 
	$(CC) $(CCFLAGS) $< 

JSphGpu_ker.o: JSphGpu_ker.cu
	$(NCC) $(NCCFLAGS) JSphGpu_ker.cu

JCellDivGpu_ker.o: JCellDivGpu_ker.cu
	$(NCC) $(NCCFLAGS) JCellDivGpu_ker.cu

JCellDivGpuSingle_ker.o: JCellDivGpuSingle_ker.cu
	$(NCC) $(NCCFLAGS) JCellDivGpuSingle_ker.cu

clean:
	rm -rf *.o $(EXECNAME) $(EXECNAME)_debug

