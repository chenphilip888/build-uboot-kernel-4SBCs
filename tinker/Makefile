export OUT=~/build-uboot-kernel-4SBCs/tinker/out
export BOOT=$(OUT)/boot.img
export ARCH=arm
export CROSS_COMPILE=/usr/local/gcc-linaro-7.3.1-2018.05-x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-

u-boot: builduboot postbuilduboot
kernel: buildkernel postbuildkernel

builduboot:
	cd debian_u-boot; \
	make clean; \
	make ARCH=arm tinker-rk3288_defconfig; \
	make all

postbuilduboot:
	cd debian_u-boot; \
	./tools/mkimage -n rk3288 -T rksd -d ./spl/u-boot-spl-dtb.bin u-boot.img; \
	cat ./u-boot.bin >> u-boot.img

buildkernel:
	cd debian_kernel; \
	cp ../patches/rk3288-miniarm.dts arch/arm/boot/dts/; \
	make clean; \
	make ARCH=arm miniarm-rk3288_defconfig; \
	make all -j4; \
	sudo make ARCH=arm INSTALL_MOD_PATH=./tmp modules_install

postbuildkernel:
	cd debian_kernel; \
	\rm -f $(BOOT); \
	mkfs.vfat -n "boot" -S 512 -C ${BOOT} 102400; \
	mmd -i ${BOOT} ::/extlinux; \
	mcopy -i ${BOOT} -s ../rk3288.conf ::/extlinux/extlinux.conf; \
	mcopy -i ${BOOT} -s ../hw_intf.conf ::; \
	mcopy -i ${BOOT} -s ./arch/arm/boot/zImage ::; \
	mcopy -i ${BOOT} -s ./arch/arm/boot/dts/rk3288-miniarm.dtb ::
