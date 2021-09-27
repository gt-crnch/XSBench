
### Compiling
This Makefile likely looks somewhat strange but this is because there is some overlap in flags between FPGA emulation, report generation, and HW bitstream generation.

To compile, pick one of the following options:
```
# CPU only
make CC=dpcpp
# FPGA emulation
make CC=dpcpp DPCPP_MODE=fpga_emu
# FPGA report
make CC=dpcpp DPCPP_MODE=fpga_report
# HW bitstream
make CC=dpcpp DPCPP_MODE=fpga_hw
```

### Running the code
Right now, the code specifies which device is used via an environment variable: XS_DEVICE

CPU code can run without this device selector
```
./XSBench -m event
```

But emulation code requires the use of this env variable:
```
./XSBench.fpga_emu -m event
...
Initializing device buffers and JIT compiling kernel...
terminate called after throwing an instance of 'cl::sycl::runtime_error'
  what():  Native API failed. Native API returns: -42 (CL_INVALID_BINARY) -42 (CL_INVALID_BINARY)
Aborted (core dumped)
#Run with the env variable
$ XS_DEVICE=FPGA_EMU ./XSBench.fpga_emu -m event
```
