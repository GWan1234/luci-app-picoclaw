# SPDX-License-Identifier: MIT
#
# Copyright (C) 2021-2026 LIKE2000-ART
# This is free software, licensed under the MIT License.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=picoclaw
PKG_VERSION:=0.1.2
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/sipeed/picoclaw/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=973faa529b144954a2a8d2212b80895641676ff0736c1488d0ad1fd70793fe53

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=LIKE2000-ART

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/sipeed/picoclaw
GO_PKG_BUILD_PKG:=github.com/sipeed/picoclaw/cmd/picoclaw
GO_PKG_TAGS:=stdjson
GO_PKG_LDFLAGS:=-s -w
GO_PKG_LDFLAGS_X:= \
	github.com/sipeed/picoclaw/cmd/picoclaw/internal.version=$(PKG_VERSION) \
	github.com/sipeed/picoclaw/cmd/picoclaw/internal.gitCommit=$(PKG_SOURCE_VERSION) \
	github.com/sipeed/picoclaw/cmd/picoclaw/internal.buildTime=$(shell date +%FT%T%z) \
	github.com/sipeed/picoclaw/cmd/picoclaw/internal.goVersion=$(GO_VERSION_ID)
CGO_ENABLED:=0

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/golang/golang-package.mk

# PicoClaw 的 main.go 使用 //go:embed workspace 嵌入工作区模板文件
# 源码通过 //go:generate cp -r ../../workspace . 来复制，但 OpenWrt 不会执行 go generate
# 因此需要在编译前手动完成这个复制步骤
define Build/Prepare
	$(call Build/Prepare/Default)
	$(CP) $(PKG_BUILD_DIR)/workspace $(PKG_BUILD_DIR)/cmd/picoclaw/workspace
endef

define Package/picoclaw
  TITLE:=PicoClaw - Ultra-Efficient AI Assistant
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  URL:=https://github.com/sipeed/picoclaw
  DEPENDS:=$(GO_ARCH_DEPENDS) +ca-bundle
endef

define Package/picoclaw/description
  PicoClaw is an ultra-lightweight personal AI Assistant built in Go.
  Runs on minimal hardware with less than 10MB RAM.
  Tiny, Fast, and Deployable anywhere — automate the mundane, unleash your creativity.
endef

define Package/picoclaw/install
	$(call GoPackage/Package/Install/Bin,$(1))

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) $(CURDIR)/files/picoclaw.init $(1)/etc/init.d/picoclaw
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) $(CURDIR)/files/picoclaw.uci-default $(1)/etc/uci-defaults/picoclaw
	$(INSTALL_DIR) $(1)/etc/config/
	$(INSTALL_CONF) $(CURDIR)/files/picoclaw.conf $(1)/etc/config/picoclaw
	$(INSTALL_DIR) $(1)/etc/picoclaw
	$(INSTALL_DIR) $(1)/usr/share/picoclaw
	$(INSTALL_DATA) $(CURDIR)/files/picoclaw-config.json $(1)/usr/share/picoclaw/picoclaw-config.json
endef

define Package/picoclaw/conffiles
/etc/config/picoclaw
/etc/picoclaw/config.json
endef

$(eval $(call GoBinPackage,picoclaw))
$(eval $(call BuildPackage,picoclaw))
