#===============================================================================
# User Options
#===============================================================================

# Compiler can be set below, or via environment variable
ifeq ($(CC),)
CC    = clang
endif
OPTIMIZE    = yes
DEBUG       = no
PROFILE     = no
MPI         = no

#===============================================================================
# Program name & source code list
#===============================================================================

program = XSBench

source = \
Main.cpp \
io.cpp \
Simulation.cpp \
GridInit.cpp \
XSutils.cpp \
Materials.cpp

obj = $(source:.cpp=.o)

#===============================================================================
# Sets Flags
#===============================================================================

# Make sure we didn't pick up lower case cc
ifeq (cc,$(CC))
CC    = clang
endif

# Standard Flags
CFLAGS := -std=c++14 -Wall

# Linker Flags
LDFLAGS = -lm

# Codeplay Compiler
ifneq (,$(findstring codeplay,$(CC)))
  CC = compute++
  CFLAGS += -sycl -sycl-driver
  LDFLAGS += -lComputeCpp
endif

# LLVM Compiler
ifneq (,$(findstring clang,$(CC)))
  CC = clang++
  CFLAGS += -fsycl
  LDFLAGS += -lOpenCL
endif

# DPC++ Compiler

# FPGA_EMULATOR
# dpcpp -fintelfpga -DFPGA_EMULATOR <target>.cpp

# Report 
#dpcpp -fintelfpga -Xshardware -Xsboard=<FPGA_BOARD> -fsycl-link=early <target>.cpp

#Hardware
#dpcpp -fintelfpga -Xshardware -Xsboard=<FPGA_BOARD> <target>.cpp


ifneq (,$(findstring dpcpp,$(CC)))
  CC = dpcpp
  CFLAGS += -fsycl-unnamed-lambda
ifneq (,$(findstring fpga_emu,$(DPCPP_MODE)))
  CFLAGS += -fintelfpga
  program = XSBench.fpga_emu
  #Report only - comment out for HW build
endif
ifneq (,$(findstring fpga_report,$(DPCPP_MODE)))
  CFLAGS += -fintelfpga -fsycl-link=early
  program = XSBench_fpga_report.a
endif
  #Report and HW
ifneq (,$(filter $(DPCPP_MODE),fpga_report fpga_hw))
  CFLAGS += -fintelfpga -Xshardware
  CFLAGS += -Xsboard=intel_s10sx_pac:pac_s10
  CFLAGS += -Xsdont-error-if-large-area-est
  program = XSBench.fpga
endif
endif

# Unused flags
# CFLAGS += -fsycl-targets=spir64_fpga-unknown-unknown-sycldevice
# CFLAGS += -fsycl -fno-sycl-early-optimizations
# LDFLAGS += -lOpenCL

# Debug Flags
ifeq ($(DEBUG),yes)
  CFLAGS += -g
  LDFLAGS  += -g
endif

# Profiling Flags
ifeq ($(PROFILE),yes)
  CFLAGS += -pg
  LDFLAGS  += -pg
endif

# Optimization Flags
ifeq ($(OPTIMIZE),yes)
  CFLAGS += -O3
endif

# MPI
ifeq ($(MPI),yes)
  CC = mpicc
  CFLAGS += -DMPI
endif

#===============================================================================
# Targets to Build
#===============================================================================

$(program): $(obj) XSbench_header.h Makefile
	$(CC) $(CFLAGS) $(obj) -o $@ $(LDFLAGS)

%.o: %.cpp XSbench_header.h Makefile
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -rf $(program) $(program).* $(obj)

edit:
	vim -p $(source) XSbench_header.h

run:
	./$(program)
