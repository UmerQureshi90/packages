include $(TOPDIR)/rules.mk

PKG_NAME:=xray-core
PKG_VERSION:=1.3.1
PKG_RELEASE:=$(AUTORELEASE)

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/XTLS/Xray-core/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=5b860144c470c3f7c6b71ffdf0c3830980f1f6ce431fbe6bf10249c1f0e991a7

PKG_MAINTAINER:=
PKG_LICENSE:=MPL-2.0
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_DIR:=$(BUILD_DIR)/Xray-core-$(PKG_VERSION)
PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

GO_PKG:=github.com/xtls/xray-core
GO_PKG_BUILD_PKG:=github.com/xtls/xray-core/main
GO_PKG_LDFLAGS:=-s -w
GO_PKG_LDFLAGS_X:= \
	$(GO_PKG)/core.build=OpenWrt \
	$(GO_PKG)/core.version=$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/xray/template
  TITLE:=A platform for building proxies to bypass network restrictions
  SECTION:=net
  CATEGORY:=Network
  URL:=https://xtls.github.io
endef

define Package/xray-core
  $(call Package/xray/template)
  DEPENDS:=$(GO_ARCH_DEPENDS) +ca-bundle
  PROVIDES:=v2ray-core
endef

define Package/xray-example
  $(call Package/xray/template)
  TITLE+= (example configs)
  DEPENDS:=xray-core
  PKGARCH:=all
endef

define Package/xray-geodata
  $(call Package/xray/template)
  TITLE+= (geodata files)
  DEPENDS:=xray-core
  PKGARCH:=all
endef

define Package/xray/description
  Xray, Penetrates Everything. Also the best v2ray-core, with XTLS support. Fully compatible configuration.
  It secures your network connections and thus protects your privacy.
endef

define Package/xray-core/description
  $(call Package/xray/description)
endef

define Package/xray-example/description
  $(call Package/xray/description)

  This includes example configuration files for xray-core.
endef

define Package/xray-geodata/description
  $(call Package/xray/description)

  This includes GEO datas used for xray-core.
endef

define Package/xray-core/conffiles
/etc/xray/
/etc/config/xray
endef

GEOIP_VER:=202102250625
GEOIP_FILE:=geoip.dat.$(GEOIP_VER)

define Download/geoip
  URL:=https://github.com/v2fly/geoip/releases/download/$(GEOIP_VER)/
  URL_FILE:=geoip.dat
  FILE:=$(GEOIP_FILE)
  HASH:=ee41b3c624e27a47b611d7cbee9da605fb9cda7c23bec1326969eb137ca6ebe7
endef

GEOSITE_VER:=20210226210728
GEOSITE_FILE:=dlc.dat.$(GEOSITE_VER)

define Download/geosite
  URL:=https://github.com/v2fly/domain-list-community/releases/download/$(GEOSITE_VER)/
  URL_FILE:=dlc.dat
  FILE:=$(GEOSITE_FILE)
  HASH:=ef9c30bacc6989a0b9fae6043dcef1ec15af96c01eddfa1f1d1ad93d14864f81
endef

define Build/Prepare
	$(call Build/Prepare/Default)
ifneq ($(CONFIG_PACKAGE_xray-geodata),)
	$(call Download,geoip)
	$(call Download,geosite)
endif
endef

define Package/xray-core/install
	$(call GoPackage/Package/Install/Bin,$(PKG_INSTALL_DIR))
	$(INSTALL_DIR) $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/main $(1)/usr/bin/xray
	$(LN) xray $(1)/usr/bin/v2ray

	$(INSTALL_DIR) $(1)/etc/xray/
	$(INSTALL_CONF) $(CURDIR)/files/config.json.example $(1)/etc/xray/

	$(INSTALL_DIR) $(1)/etc/config/
	$(INSTALL_CONF) $(CURDIR)/files/xray.conf $(1)/etc/config/xray
	$(INSTALL_DIR) $(1)/etc/init.d/
	$(INSTALL_BIN) $(CURDIR)/files/xray.init $(1)/etc/init.d/xray

	$(INSTALL_DIR) $(1)/etc/capabilities/
	$(INSTALL_DATA) $(CURDIR)/files/xray.capabilities $(1)/etc/capabilities/xray.json
endef

define Package/xray-example/install
	$(INSTALL_DIR) $(1)/etc/xray/
	$(INSTALL_CONF) $(CURDIR)/files/vpoint_socks_vmess.json $(1)/etc/xray/
	$(INSTALL_CONF) $(CURDIR)/files/vpoint_vmess_freedom.json $(1)/etc/xray/
endef

define Package/xray-geodata/install
	$(INSTALL_DIR) $(1)/usr/share/xray/
	$(INSTALL_DATA) $(DL_DIR)/$(GEOIP_FILE) $(1)/usr/share/xray/geoip.dat
	$(INSTALL_DATA) $(DL_DIR)/$(GEOSITE_FILE) $(1)/usr/share/xray/geosite.dat
endef

$(eval $(call BuildPackage,xray-core))
$(eval $(call BuildPackage,xray-example))
$(eval $(call BuildPackage,xray-geodata))
