export OUT=~/build-uboot-kernel-4SBCs/rk3328/out
export BOOT=$(OUT)/boot.img
export ARCH=arm64
export CROSS_COMPILE=/usr/local/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-

u-boot: cleanuboot builduboot postbuilduboot
kernel: buildkernel postbuildkernel

cleanuboot:
	\rm -f $(OUT)/idbloader.img
	\rm -f $(OUT)/rk3328_loader_ddr786_v1.06.243.bin
	\rm -f $(OUT)/trust.img
	\rm -f $(OUT)/uboot.img

builduboot:
	cd u-boot; \
	make clean; \
	make ARCH=arm64 roc-cc-rk3328_defconfig; \
	make all

postbuilduboot:
	cd u-boot; \
	../rkbin/tools/loaderimage --pack --uboot ./u-boot-dtb.bin uboot.img 0x200000; \
	./tools/mkimage -n rk3328 -T rksd -d ../rkbin/rk33/rk3328_ddr_786MHz_v1.06.bin idbloader.img; \
	cat ../rkbin/rk33/rk3328_miniloader_v2.43.bin >> idbloader.img; \
	cp idbloader.img ${OUT}/; \
	cp ../rkbin/rk33/rk3328_loader_ddr786_v1.06.243.bin ${OUT}/; \
	../rkbin/tools/trust_merger ../trust.ini; \
	cp uboot.img ${OUT}/; \
	cp trust.img ${OUT}/

buildkernel:
	cd kernel; \
	cp ../patches/fireflyrk3328_linux_defconfig arch/arm64/configs/; \
	cp ../patches/rk3328-roc-cc.dts arch/arm64/boot/dts/rockchip/; \
	cp ../patches/rk3328.dtsi arch/arm64/boot/dts/rockchip/; \
	cp ../patches/rk3328-box-plus-dram-timing.dtsi arch/arm64/boot/dts/rockchip/; \
	cp ../patches/rk3328-dram-default-timing.dtsi arch/arm64/boot/dts/rockchip/; \
	cp ../patches/Makefile arch/arm64/boot/dts/rockchip/; \
	make clean; \
	make ARCH=arm64 fireflyrk3328_linux_defconfig; \
	make all -j4; \
	sudo make ARCH=arm64 INSTALL_MOD_PATH=./tmp modules_install

postbuildkernel:
	cd kernel; \
	\rm -f $(BOOT); \
	mkfs.vfat -n "boot" -S 512 -C ${BOOT} 102400; \
	mmd -i ${BOOT} ::/extlinux; \
	mcopy -i ${BOOT} -s ../rk3328.conf ::/extlinux/extlinux.conf; \
	mcopy -i ${BOOT} -s ./arch/arm64/boot/Image ::; \
	mcopy -i ${BOOT} -s ./arch/arm64/boot/dts/rockchip/rk3328-roc-cc.dtb ::
