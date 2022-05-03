export OUT=~/build-uboot-kernel-4SBCs/radxa/out
export BOOT=$(OUT)/boot.img
export ARCH=arm64
export CROSS_COMPILE=/usr/local/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-

u-boot: cleanuboot builduboot postbuilduboot
kernel: buildkernel postbuildkernel

cleanuboot:
	\rm -f $(OUT)/idbloader.img
	\rm -f $(OUT)/rk3399_loader_v1.20.119.bin
	\rm -f $(OUT)/trust.img
	\rm -f $(OUT)/uboot.img

builduboot:
	cd u-boot; \
	make clean; \
	make ARCH=arm64 rock-pi-4b-rk3399_defconfig; \
	make all

postbuilduboot:
	cd u-boot; \
	../rkbin/tools/loaderimage --pack --uboot ./u-boot-dtb.bin uboot.img 0x200000 --size 1024 1; \
	./tools/mkimage -n rk3399 -T rksd -d ../rkbin/bin/rk33/rk3399_ddr_800MHz_v1.20.bin idbloader.img; \
	cat ../rkbin/bin/rk33/rk3399_miniloader_v1.19.bin >> idbloader.img; \
	cp idbloader.img ${OUT}/; \
	cp ../rkbin/bin/rk33/rk3399_loader_v1.20.119.bin ${OUT}/; \
	../rkbin/tools/trust_merger --size 1024 1 ../trust.ini; \
	cp uboot.img ${OUT}/; \
	cp trust.img ${OUT}/

buildkernel:
	cd kernel; \
	cp ../patches/rockpi-4-linux.dtsi arch/arm64/boot/dts/rockchip; \
	make clean; \
	make ARCH=arm64 rockchip_linux_defconfig; \
	make -j4; \
	sudo make ARCH=arm64 INSTALL_MOD_PATH=./tmp modules_install

postbuildkernel:
	cd kernel; \
	\rm -f $(BOOT); \
	mkfs.vfat -n "boot" -S 512 -C ${BOOT} 512000; \
	mmd -i ${BOOT} ::/extlinux; \
	mcopy -i ${BOOT} -s ../rk3399.conf ::/extlinux/extlinux.conf; \
	mcopy -i ${BOOT} -s ./arch/arm64/boot/Image ::; \
	mcopy -i ${BOOT} -s ./arch/arm64/boot/dts/rockchip/rockpi-4b-linux.dtb ::
