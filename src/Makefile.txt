ifneq ($(KERNELRELEASE),)
# Inside kernel build system

obj-m += qnap8528.o

else
# Outside kernel build system

KERNEL_SOURCE ?= /build/linux
CROSS_COMPILE ?= /usr/local/x86_64-pc-linux-gnu/bin/x86_64-pc-linux-gnu-
PWD := $(shell pwd)

all: modules

modules:
	$(MAKE) -C $(KERNEL_SOURCE) M=$(PWD) CROSS_COMPILE=$(CROSS_COMPILE) modules

clean:
	$(MAKE) -C $(KERNEL_SOURCE) M=$(PWD) CROSS_COMPILE=$(CROSS_COMPILE) clean
	rm -f *.o *.ko *.mod.* Module.symvers modules.order

install: modules
	$(MAKE) -C $(KERNEL_SOURCE) M=$(PWD) CROSS_COMPILE=$(CROSS_COMPILE) modules_install

uninstall:
	rm -f /lib/modules/*/kernel/drivers/platform/qnap8528.ko

help:
	@echo "QNAP8528 EC Kernel Module Makefile"
	@echo ""
	@echo "Available targets:"
	@echo "  make                 - Build kernel modules"
	@echo "  make clean           - Remove build artifacts"
	@echo "  make install         - Build and install modules"
	@echo "  make uninstall       - Remove installed modules"
	@echo "  make help            - Display this help message"
	@echo ""
	@echo "Environment variables:"
	@echo "  KERNEL_SOURCE        - Path to Linux kernel source (default: /build/linux)"
	@echo "  CROSS_COMPILE        - Cross-compiler prefix"

.PHONY: all modules clean install uninstall help

endif
