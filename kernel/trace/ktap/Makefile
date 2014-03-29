# Define amalg to enable amalgamation build, This compiles the ktapvm as
# one huge C file and allows GCC to generate faster and shorter code. Also,
# this requires lots of memory during the build.
# Recommend to use amalgmation build as default.
amalg = 1

# Do not instrument the tracer itself:
ifdef CONFIG_FUNCTION_TRACER
ORIG_CFLAGS := $(KBUILD_CFLAGS)
KBUILD_CFLAGS = $(subst -pg,,$(ORIG_CFLAGS))
endif

all: mod

KTAP_LIBS = -lpthread

LIB_OBJS += lib_base.o lib_kdebug.o lib_timer.o lib_ansi.o lib_table.o \
		lib_net.o

ifndef amalg
RUNTIME_OBJS += ktap.o kp_bcread.o kp_obj.o kp_str.o kp_mempool.o \
		kp_tab.o kp_vm.o kp_transport.o kp_events.o $(LIB_OBJS)
else
RUNTIME_OBJS += amalg.o
endif

obj-m		+= ktapvm.o
ktapvm-y	:= $(RUNTIME_OBJS)

KVERSION ?= $(shell uname -r)
KERNEL_SRC ?= /lib/modules/$(KVERSION)/build
PWD := $(shell pwd)
mod:
	$(MAKE) -C $(KERNEL_SRC) M=$(PWD) modules

modules_install:
	$(MAKE) -C $(KERNEL_SRC) M=$(PWD) modules_install

load:
	insmod ktapvm.ko

unload:
	rmmod ktapvm

reload:
	make unload; make load

clean:
	$(MAKE) -C $(KERNEL_SRC) M=$(PWD) clean

