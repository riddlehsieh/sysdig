always += probe.o

LLC ?= llc
CLANG ?= clang

KERNELDIR ?= /lib/modules/$(shell uname -r)/build

# DEBUG = -DBPF_DEBUG

all:
	$(MAKE) -C $(KERNELDIR) M=$$PWD

clean:
	$(MAKE) -C $(KERNELDIR) M=$$PWD clean
	@rm -f *~

$(obj)/probe.o: $(src)/probe.c \
		$(src)/bpf_helpers.h \
		$(src)/filler_helpers.h \
		$(src)/fillers.h \
		$(src)/maps.h \
		$(src)/plumbing_helpers.h \
		$(src)/quirks.h \
		$(src)/ring_helpers.h \
		$(src)/types.h
	$(CLANG) $(LINUXINCLUDE) \
		$(KBUILD_CPPFLAGS) \
		$(DEBUG) \
		-D__KERNEL__ \
		-D__BPF_TRACING__ \
		-Wno-gnu-variable-sized-type-not-at-end \
		-Wno-address-of-packed-member \
		-fno-jump-tables \
		-Wno-tautological-compare \
		-O2 -g -emit-llvm -c $< -o $(patsubst %.o,%.ll,$@)
	$(LLC) -march=bpf -filetype=obj -o $@ $(patsubst %.o,%.ll,$@)
