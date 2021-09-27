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
ifneq (,$(filter $(DPCPP_MODE),fpga_emu,fpga_report fpga_hw))
  CFLAGS += -fintelfpga
endif
# Emulation only flags
ifneq (,$(findstring fpga_emu,$(DPCPP_MODE)))
  program = XSBench.fpga_emu
endif
#Report and HW shared flags
ifneq (,$(filter $(DPCPP_MODE),fpga_report fpga_hw))
  CFLAGS += -Xshardware -Xsboard=intel_s10sx_pac:pac_s10
endif
# Report only flags
ifneq (,$(findstring fpga_report,$(DPCPP_MODE)))
  CFLAGS += -fsycl-link
endif
# HW only flags
ifneq (,$(findstring fpga_hw,$(DPCPP_MODE)))
  CFLAGS += -fintelfpga -Xsdont-error-if-large-area-est
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

report: $(obj) XSbench_header.h Makefile
	$(CC) $(CFLAGS) $(obj)

%.o: %.cpp XSbench_header.h Makefile
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -rf $(program) $(program).* $(obj)

edit:
	vim -p $(source) XSbench_header.h

run:
	./$(program)
