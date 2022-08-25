How to build

repo init -u git@github.com:changxiangzhong/rockpi4-manifests.git -b rockpi-box-9.0 -m rockpi-release.xml
repo sync -d --no-tags

# Compile U-Boot
cd u-boot/
./make.sh rk3399

# Compile kernel
make chang_defconfig
make rk3399-chang.img -j32

# Compile AOSP
source build/envsetup.sh
lunch 57
make -j 32

# Rockchip specific shell to make .img files
./mkimage.sh

# Link box images to rockdev/Image
ln -s RKTools/linux/Linux_Pack_Firmware/rockdev/ rockdev
cd rockdev
ln -s Image-rk3399_box/ Image

# Generate final gpt.img
./android-gpt.sh

# Optional. write image from a remote server to a local disk
ssh ryzen.mynetgear.com "dd if=/home/chang/bulk2//rockpi4-android9/rockdev/Image/gpt.img" | sudo sh -c 'dd of=/dev/rdisk2 status=progress bs=4M oflag=direct'

