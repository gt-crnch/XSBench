#===============================================================================
# User Options
#===============================================================================

CC          = llvm
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

# Standard Flags
CFLAGS := -std=c++14 -Wall

# Linker Flags
LDFLAGS = -lm

# Codeplay Compiler
ifeq ($(CC),codeplay)
  CC = compute++
  CFLAGS += -sycl -sycl-driver
  LDFLAGS += -lComputeCpp
endif

# LLVM Compiler
ifeq ($(CC),llvm)
  CC = clang++
  CFLAGS += -fsycl
  LDFLAGS += -lOpenCL
endif

# DPC++ Compiler

# FPGA_EMULATOR
# dpcpp -fintelfpga -DFPGA_EMULATOR <target>.cpp

# FPGA_EMULATOR
# dpcpp -fintelfpga -DFPGA_EMULATOR <target>.cpp

# Report 
#dpcpp -fintelfpga -Xshardware -Xsboard=<FPGA_BOARD> -fsycl-link=early <target>.cpp

#Hardware
#dpcpp -fintelfpga -Xshardware -Xsboard=<FPGA_BOARD> <target>.cpp

#Profile on the Hardware
#dpcpp -fintelfpga -Xshardware -Xsboard=<FPGA_BOARD> -Xsprofile <target>.cpp

ifneq (,$(findstring dpcpp,$(CC)))
  CC = dpcpp
  CFLAGS += -fsycl-unnamed-lambda
  #By default we build the CPU version - other flags will generate differently named exe files
  program = XSBench.cpu

ifneq (,$(filter $(DPCPP_MODE),fpga_emu fpga_report fpga_hw fpga_profile))
  CFLAGS += -fintelfpga
endif
# CPU flags
#ifneq (,$(findstring cpu,$(DPCPP_MODE)))
#  program = XSBench.cpu
#endif
# Emulation only flags
ifneq (,$(findstring fpga_emu,$(DPCPP_MODE)))
  program = XSBench.fpga_emu
endif
# Report only flags
ifneq (,$(findstring fpga_report,$(DPCPP_MODE)))
  CFLAGS += -fsycl-link -fsycl -fsycl-unnamed-lambda -g  -fno-sycl-early-optimizations
  program = XSBench.fpga_report
endif
#Report and HW shared flags
ifneq (,$(filter $(DPCPP_MODE),fpga_report fpga_hw fpga_profile))
  CFLAGS += -Xshardware -Xsboard=intel_s10sx_pac:pac_s10
endif
# HW only flags
ifneq (,$(findstring fpga_hw,$(DPCPP_MODE)))
  CFLAGS += -Xsdont-error-if-large-area-est
  program = XSBench.fpga
endif
# HW profile only flags
ifneq (,$(findstring fpga_profile,$(DPCPP_MODE)))
  CFLAGS += -Xsprofile
  program = XSBench.fpga.profile
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
	$(CC) $(CFLAGS) -o $@ $(obj) $(LDFLAGS)

%.o: %.cpp XSbench_header.h Makefile
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -rf $(program) $(program).* $(obj)

edit:
	vim -p $(source) XSbench_header.h

run:
	./$(program)
