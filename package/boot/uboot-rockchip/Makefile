#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_VERSION:=2023.07-rc3
PKG_RELEASE:=1

PKG_HASH:=42ec083674bd30fa4c16e78c3f5f453c08d9f737595a77b5dda595ac3a09d82f

PKG_MAINTAINER:=Tobias Maedel <openwrt@tbspace.de>

include $(INCLUDE_DIR)/u-boot.mk
include $(INCLUDE_DIR)/package.mk

define U-Boot/Default
  BUILD_TARGET:=rockchip
  UENV:=default
  HIDDEN:=1
endef


# RK3328 boards

define U-Boot/rk3328/Default
  BUILD_SUBTARGET:=armv8
  DEPENDS:=+PACKAGE_u-boot-$(1):arm-trusted-firmware-rockchip
  ATF:=rk3328_bl31.elf
  OF_PLATDATA:=$(1)
endef

define U-Boot/nanopi-r2c-rk3328
  $(U-Boot/rk3328/Default)
  NAME:=NanoPi R2C
  BUILD_DEVICES:= \
    friendlyarm_nanopi-r2c
endef

define U-Boot/nanopi-r2s-rk3328
  $(U-Boot/rk3328/Default)
  NAME:=NanoPi R2S
  BUILD_DEVICES:= \
    friendlyarm_nanopi-r2s
endef

define U-Boot/orangepi-r1-plus-rk3328
  $(U-Boot/rk3328/Default)
  NAME:=Orange Pi R1 Plus
  BUILD_DEVICES:= \
    xunlong_orangepi-r1-plus
endef

define U-Boot/orangepi-r1-plus-lts-rk3328
  $(U-Boot/rk3328/Default)
  NAME:=Orange Pi R1 Plus LTS
  BUILD_DEVICES:= \
    xunlong_orangepi-r1-plus-lts
endef

define U-Boot/roc-cc-rk3328
  $(U-Boot/rk3328/Default)
  NAME:=ROC-RK3328-CC
  BUILD_DEVICES:= \
    firefly_roc-rk3328-cc
endef

# RK3399 boards

define U-Boot/rk3399/Default
  BUILD_SUBTARGET:=armv8
  DEPENDS:=+PACKAGE_u-boot-$(1):arm-trusted-firmware-rockchip
  ATF:=rk3399_bl31.elf
endef

define U-Boot/nanopi-r4s-rk3399
  $(U-Boot/rk3399/Default)
  NAME:=NanoPi R4S
  BUILD_DEVICES:= \
    friendlyarm_nanopi-r4s
endef

define U-Boot/rock-pi-4-rk3399
  $(U-Boot/rk3399/Default)
  NAME:=Rock Pi 4
  BUILD_DEVICES:= \
    radxa_rock-pi-4a
endef

define U-Boot/rockpro64-rk3399
  $(U-Boot/rk3399/Default)
  NAME:=RockPro64
  BUILD_DEVICES:= \
    pine64_rockpro64
endef


# RK3588 boards

define U-Boot/nanopi-r6s-rk3588
  BUILD_SUBTARGET:=armv8
  NAME:=NanoPi R6S
  BUILD_DEVICES:= \
    friendlyarm_nanopi-r6s
  DEPENDS:=+PACKAGE_u-boot-nanopi-r6s-rk3588:arm-trusted-firmware-rockchip
  PKG_BUILD_DEPENDS:=arm-trusted-firmware-rockchip
  ATF:=rk3588_bl31.elf
  ROCKCHIP_TPL:=rk3588_ddr_lp4_2112MHz_lp5_2736MHz_v1.09.bin
endef


UBOOT_TARGETS := \
  nanopi-r4s-rk3399 \
  rock-pi-4-rk3399 \
  rockpro64-rk3399 \
  nanopi-r2c-rk3328 \
  nanopi-r2s-rk3328 \
  orangepi-r1-plus-rk3328 \
  orangepi-r1-plus-lts-rk3328 \
  roc-cc-rk3328 \
  nanopi-r6s-rk3588

UBOOT_CONFIGURE_VARS += USE_PRIVATE_LIBGCC=yes

UBOOT_MAKE_FLAGS += \
  BL31=$(STAGING_DIR_IMAGE)/$(ATF)
# ifneq ($(ROCKCHIP_TPL),)
  UBOOT_MAKE_FLAGS += \
    ROCKCHIP_TPL=$(STAGING_DIR_IMAGE)/$(ROCKCHIP_TPL)
  UBOOT_CONFIGURE_VARS += \
    ROCKCHIP_TPL=$(STAGING_DIR_IMAGE)/$(ROCKCHIP_TPL)
  UBOOT_MAKE_FLAGS += \
    ARCH=arm
  UBOOT_CONFIGURE_VARS += \
    ARCH=arm
# endif


define Build/Configure
	$(call Build/Configure/U-Boot)

ifneq ($(OF_PLATDATA),)
	mkdir -p $(PKG_BUILD_DIR)/tpl/dts
	mkdir -p $(PKG_BUILD_DIR)/include/generated

	$(CP) $(PKG_BUILD_DIR)/of-platdata/$(OF_PLATDATA)/dt-plat.c $(PKG_BUILD_DIR)/tpl/dts/dt-plat.c
	$(CP) $(PKG_BUILD_DIR)/of-platdata/$(OF_PLATDATA)/dt-structs-gen.h $(PKG_BUILD_DIR)/include/generated/dt-structs-gen.h
	$(CP) $(PKG_BUILD_DIR)/of-platdata/$(OF_PLATDATA)/dt-decl.h $(PKG_BUILD_DIR)/include/generated/dt-decl.h
endif

#	$(SED) 's#CONFIG_MKIMAGE_DTC_PATH=.*#CONFIG_MKIMAGE_DTC_PATH="$(PKG_BUILD_DIR)/scripts/dtc/dtc"#g' $(PKG_BUILD_DIR)/.config
endef

# use host dtc
define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		CROSS_COMPILE=$(TARGET_CROSS) \
		$(UBOOT_MAKE_FLAGS)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(STAGING_DIR_IMAGE)
	$(CP) $(PKG_BUILD_DIR)/idbloader.img $(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-idbloader.img
	$(CP) $(PKG_BUILD_DIR)/u-boot.itb $(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-u-boot.itb
endef

define Package/u-boot/install/default
endef

$(eval $(call BuildPackage/U-Boot))
