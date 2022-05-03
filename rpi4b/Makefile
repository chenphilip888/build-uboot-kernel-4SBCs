export KERNEL=kernel7l
export OUT=~/build-uboot-kernel-4SBCs/rpi4b/out
export BOOT=$(OUT)/boot.img
export ARCH=arm
export CROSS_COMPILE=/usr/local/gcc-linaro-7.3.1-2018.05-x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-

kernel: buildkernel postbuildkernel

buildkernel:
	cd linux; \
	cp ../patches/bcm2711_defconfig arch/arm/configs/; \
	make clean; \
	make ARCH=arm bcm2711_defconfig; \
	make all -j4; \
	sudo make ARCH=arm INSTALL_MOD_PATH=./tmp modules_install

postbuildkernel:
	cd linux; \
	\rm -f $(BOOT); \
	mkfs.vfat -n "boot" -S 512 -C ${BOOT} 256000; \
	mmd -i ${BOOT} ::/overlays; \
	mcopy -i ${BOOT} -s ../firmware/fixup4.dat ::; \
	mcopy -i ${BOOT} -s ../firmware/start4.elf ::; \
	mcopy -i ${BOOT} -s ../firmware/bootcode.bin ::; \
	mcopy -i ${BOOT} -s ../firmware/cmdline.txt ::; \
	mcopy -i ${BOOT} -s ../firmware/config.txt ::; \
	mcopy -i ${BOOT} -s ./arch/arm/boot/Image ::/kernel7l.img; \
	mcopy -i ${BOOT} -s ./arch/arm/boot/dts/bcm2711-rpi-4-b.dtb ::; \
	mcopy -i ${BOOT} -s ./arch/arm/boot/dts/overlays/*.dtb* ::/overlays; \
	mcopy -i ${BOOT} -s ./arch/arm/boot/dts/overlays/README ::/overlays
