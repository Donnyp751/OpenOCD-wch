jimsh ?= ../jimsh
tclsh ?= tclsh

test:
	@LD_LIBRARY_PATH="/home/dposterick/Downloads/riscv-openocd-wch/jimtcl:$(LD_LIBRARY_PATH)" "$(jimsh)" "/home/dposterick/Downloads/riscv-openocd-wch/jimtcl/tests/runall.tcl"

tcl:
	@rc=0; for i in "/home/dposterick/Downloads/riscv-openocd-wch/jimtcl"/tests/*.test; do "$(tclsh)" -encoding utf-8 $$i || rc=$?; done; exit $$rc

clean:
	rm -f gorp.file2 gorp.file sleepx test1 exec.tmp1
