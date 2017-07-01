#
# Copyright (C) 2017 Brian S. Julin <bri@abrij.org>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=ebtables-dhcpsnoop
PKG_RELEASE:=4

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/skids/ebtables-dhcpsnooping.git
PKG_SOURCE_DATE:=2017-06-30
PKG_SOURCE_VERSION:=4d87975976781010eef8361dd89d410d821b8bde
#PKG_MIRROR_HASH:=
PKG_MAINTAINER:=Brian S. Julin <bri@abrij.org>

PKG_LICENSE:=GPL-3.0+
PKG_LICENSE_FILES:=COPYING

PKG_FIXUP:=autoreconf


include $(INCLUDE_DIR)/download.mk
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-${PKG_VERSION}
include $(INCLUDE_DIR)/package.mk

TARGET_CPPFLAGS := -D_GNU_SOURCE -I$(STAGING_DIR)/usr/lib/libnet-1.2.x/include/ $(TARGET_CPPFLAGS)
# Upstream author seems to want this at 2.  Going to go error
# on the side of security here for now.  And massage the field
# name in netinet/udp.h with _GNU_SOURCE.
TARGET_CFLAGS := -D_GNU_SOURCE $(filter-out -D_FORTIFY_SOURCE=1,$(TARGET_CFLAGS))
TARGET_CFLAGS += -DCHAINNAME=\"\\\"dhcpsnooping\\\"\"
TARGET_CFLAGS += -DNFSETPREFIX=\"\\\"leases-\\\"\"
TARGET_CFLAGS += -DNFTABLE=\"\\\"bridge\\ dhcpsnooping\\\"\"
TARGET_CFLAGS += -DNFTABLES=\"\\\"nft\\\"\"
TARGET_CFLAGS += -DEBTABLES=\"\\\"ebtables\\\"\"
TARGET_CFLAGS += -DMYCNF=\"\\\"/etc/dhcpsnooping.my.cnf\\\"\"
TARGET_CFLAGS += -DMYSQLDB=\"\\\"dhcpsnooping\\\"\"
TARGET_CFLAGS += -DMYSQLLEASETABLE=\"\\\"leases\\\"\"
TARGET_CFLAGS += -DMYSQLGROUP=\"\\\"dhcpsnooping\\\"\"
TARGET_CFLAGS += -DPGSQLLEASETABLE=\"\\\"leases\\\"\"
TARGET_CFLAGS += -DPGSQLSERVICE=\"\\\"/etc/dhcpsnooping.pg.cnf\\\"\"
TARGET_CFLAGS += -DPGSQLSERVICENAME=\"\\\"dhcpsnooping\\\"\"
TARGET_CFLAGS += -DNETWORKPORT=1000
TARGET_CFLAGS += -DNETWORKADDR=\"\\\"127.0.0.0\\\"\"
TARGET_CFLAGS += -DNETWORKMASK=\"\\\"255.0.0.0\\\"\"

TARGET_LDFLAGS += -L$(STAGING_DIR)/usr/lib/mysql

define Package/ebtables-dhcpsnooping-mysql-ap/Prepare
        mkdir -p $(PKG_BUILD_DIR)
endef

define Package/ebtables-dhcpsnooping-mysql-ap/Default
endef

define Package/ebtables-dhcpsnooping-mysql-ap/Configure
endef

define Package/ebtables-dhcpsnooping-mysql-ap
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Firewall
  TITLE:=DHCP Snooping Daemon (ebtables-based)
  URL:=https://github.com/skids/ebtables-dhcpsnooping
  VARIANT:=ebtmysql
  DEPENDS:=+ebtables +kmod-ebtables-ipv4 +kmod-ebtables-watchers +kmod-nfnetlink-log +libnl-genl +libnl-nf +libnl-route +libmysqlclient +libnet-1.2.x
  BUILD_DEPENDS:=libnet-1.2.x libmysqlclient
endef

define Package/ebtables-dhcpsnooping-mysql-ap/description
  A DHCP snooping system which monitors transit DHCP packets and
  based on completed DHCP transactions, interacts with ebtables rules
  to prevent spoofing of IP addresses, ARP responses, or DHCP transactions.

  The system supports multiple SSIDs across multiple APs through a combination
  of IP broadcast packets for fast but unreliable convergence, and a database
  backend for fallback reliability.  APs should have a control plane that is in
  a single broadcast domain, and name bridge interfaces consistently by SSID.

  The ebtables-dhcpsnooping-mysql-ap package includes only those parts
  of the ebtables-dhcpsnooping suite that an AP needs.  It is assumed that
  a mysql database is available elsewhere on your control plane to keep
  track of leases across AP reboots or when broadcasts fail to promulgate
  roaming client data to other APs.
endef

define Package/ebtables-dhcpsnooping-mysql-ap/conffiles
/etc/dhcpsnoopingd.my.cnf
/etc/config/dhcpsnooping
endef

define Package/ebtables-dhcpsnooping-mysql-ap/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/dhcpsnoopingd $(1)/usr/sbin/dhcpsnoopingd
	$(INSTALL_DIR) $(1)/etc
	$(INSTALL_CONF) ./files/etc_my_cnf $(1)/etc/dhcpsnoopingd.my.cnf
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/conf_dhcpsnooping_mysql $(1)/etc/config/dhcpsnooping
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/initd_dhcpsnooping $(1)/etc/init.d/dhcpsnooping
endef

define Package/ebtables-dhcpsnooping-mysql-ap/Prepare
        mkdir -p $(PKG_BUILD_DIR)
endef

define Package/ebtables-dhcpsnooping-pgsql-ap/Default
endef

define Package/ebtables-dhcpsnooping-pgsql-ap
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Firewall
  TITLE:=DHCP Snooping Daemon (ebtables-based)
  URL:=https://github.com/skids/ebtables-dhcpsnooping
  VARIANT:=ebtpgsql
  DEPENDS:=+ebtables +kmod-ebtables-ipv4 +kmod-ebtables-watchers +kmod-nfnetlink-log +libnl-genl +libnl-nf +libnl-route +libpq +libnet-1.2.x
  BUILD_DEPENDS:=libnet-1.2.x libpq
endef

define Package/ebtables-dhcpsnooping-pgsql-ap/description
  A DHCP snooping system which monitors transit DHCP packets and
  based on completed DHCP transactions, interacts with ebtables rules
  to prevent spoofing of IP addresses, ARP responses, or DHCP transactions.

  The system supports multiple SSIDs across multiple APs through a combination
  of IP broadcast packets for fast but unreliable convergence, and a database
  backend for fallback reliability.  APs should have a control plane that is in
  a single broadcast domain, and name bridge interfaces consistently by SSID.

  The ebtables-dhcpsnooping-pgsql-ap package includes only those parts
  of the ebtables-dhcpsnooping suite that an AP needs.  It is assumed that
  a postgres database is available elsewhere on your control plane to keep
  track of leases across AP reboots or when broadcasts fail to promulgate
  roaming client data to other APs.
endef

define Package/ebtables-dhcpsnooping-pgsql-ap/conffiles
/etc/dhcpsnoopingd.pg.cnf
/etc/config/dhcpsnooping
endef

define Package/ebtables-dhcpsnooping-pgsql-ap/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/dhcpsnoopingd $(1)/usr/sbin/dhcpsnoopingd
	$(INSTALL_DIR) $(1)/etc
	$(INSTALL_CONF) ./files/etc_pg_cnf $(1)/etc/dhcpsnoopingd.pg.cnf
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/conf_dhcpsnooping_pgsql $(1)/etc/config/dhcpsnooping
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/initd_dhcpsnooping $(1)/etc/init.d/dhcpsnooping
endef

define Package/nftables-dhcpsnooping-mysql-ap/Prepare
        mkdir -p $(PKG_BUILD_DIR)
endef

define Package/nftables-dhcpsnooping-mysql-ap/Default
endef

define Package/nftables-dhcpsnooping-mysql-ap/Configure
endef

define Package/nftables-dhcpsnooping-mysql-ap
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Firewall
  TITLE:=DHCP Snooping Daemon (nftables-based)
  URL:=https://github.com/skids/nftables-dhcpsnooping
  VARIANT:=nftmysql
  DEPENDS:=+nftables +kmod-nft-core +kmod-nfnetlink-log +libnl-genl +libnl-nf +libnl-route +libmysqlclient +libnet-1.2.x
  BUILD_DEPENDS:=libnet-1.2.x libmysqlclient
endef

define Package/nftables-dhcpsnooping-mysql-ap/description
  A DHCP snooping system which monitors transit DHCP packets and
  based on completed DHCP transactions, interacts with nftables rules
  to prevent spoofing of IP addresses, ARP responses, or DHCP transactions.

  The system supports multiple SSIDs across multiple APs through a combination
  of IP broadcast packets for fast but unreliable convergence, and a database
  backend for fallback reliability.  APs should have a control plane that is in
  a single broadcast domain, and name bridge interfaces consistently by SSID.

  The nftables-dhcpsnooping-mysql-ap package includes only those parts
  of the nftables-dhcpsnooping suite that an AP needs.  It is assumed that
  a mysql database is available elsewhere on your control plane to keep
  track of leases across AP reboots or when broadcasts fail to promulgate
  roaming client data to other APs.
endef

define Package/nftables-dhcpsnooping-mysql-ap/conffiles
/etc/dhcpsnoopingd.my.cnf
/etc/config/dhcpsnooping
endef

define Package/nftables-dhcpsnooping-mysql-ap/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/dhcpsnoopingd $(1)/usr/sbin/dhcpsnoopingd
	$(INSTALL_DIR) $(1)/etc
	$(INSTALL_CONF) ./files/etc_my_cnf $(1)/etc/dhcpsnoopingd.my.cnf
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/conf_dhcpsnooping_mysql $(1)/etc/config/dhcpsnooping
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/initd_dhcpsnooping_nf $(1)/etc/init.d/dhcpsnooping
endef

define Package/nftables-dhcpsnooping-mysql-ap/Prepare
        mkdir -p $(PKG_BUILD_DIR)
endef

define Package/nftables-dhcpsnooping-pgsql-ap/Default
endef

define Package/nftables-dhcpsnooping-pgsql-ap
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Firewall
  TITLE:=DHCP Snooping Daemon (nftables-based)
  URL:=https://github.com/skids/nftables-dhcpsnooping
  VARIANT:=nftpgsql
  DEPENDS:=+nftables +kmod-nft-core +kmod-nfnetlink-log +kmod-nfnetlink-log +libnl-genl +libnl-nf +libnl-route +libpq +libnet-1.2.x
  BUILD_DEPENDS:=libnet-1.2.x libpq
endef

define Package/nftables-dhcpsnooping-pgsql-ap/description
  A DHCP snooping system which monitors transit DHCP packets and
  based on completed DHCP transactions, interacts with nftables rules
  to prevent spoofing of IP addresses, ARP responses, or DHCP transactions.

  The system supports multiple SSIDs across multiple APs through a combination
  of IP broadcast packets for fast but unreliable convergence, and a database
  backend for fallback reliability.  APs should have a control plane that is in
  a single broadcast domain, and name bridge interfaces consistently by SSID.

  The nftables-dhcpsnooping-pgsql-ap package includes only those parts
  of the nftables-dhcpsnooping suite that an AP needs.  It is assumed that
  a postgres database is available elsewhere on your control plane to keep
  track of leases across AP reboots or when broadcasts fail to promulgate
  roaming client data to other APs.
endef

define Package/nftables-dhcpsnooping-pgsql-ap/conffiles
/etc/dhcpsnoopingd.pg.cnf
/etc/config/dhcpsnooping
endef

define Package/nftables-dhcpsnooping-pgsql-ap/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/dhcpsnoopingd $(1)/usr/sbin/dhcpsnoopingd
	$(INSTALL_DIR) $(1)/etc
	$(INSTALL_CONF) ./files/etc_pg_cnf $(1)/etc/dhcpsnoopingd.pg.cnf
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/conf_dhcpsnooping_pgsql $(1)/etc/config/dhcpsnooping
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/initd_dhcpsnooping_nf $(1)/etc/init.d/dhcpsnooping
endef

CONFIGURE_ARGS += --prefix=/usr --enable-roaming --enable-debug
ifeq ($(BUILD_VARIANT),ebtmysql)
  CONFIGURE_ARGS += --enable-ebtables --enable-mysql
endif
ifeq ($(BUILD_VARIANT),ebtpgsql)
  CONFIGURE_ARGS += --enable-ebtables --enable-pgsql
  # Work around some brokenness in pkg-config test for libpq
  # These do not get checked until after pkconfig so do not prevent it.
  # --with-pgsql-include-path=$(STAGING_DIR)/usr/include --with-pgsql-lib-path=$(STAGING_DIR)/usr/lib
  # These do not work for some unknown reason
  #CONFIGURE_VARS+=PGSQL_CFLAGS="-I$(STAGING_DIR)/usr/include -L$(STAGING_DIR)/usr/lib"
  #CONFIGURE_VARS+=PGSQL_LIBS="-lpq"
  # Yes, PGSQL_CFLAGS adds nothing we do not already have but
  # must be present to prevent pkg-config
  export PGSQL_CFLAGS="-I$(STAGING_DIR)/usr/include -L$(STAGING_DIR)/usr/lib"
  export PGSQL_LIBS="-lpq"
endif
ifeq ($(BUILD_VARIANT),nftmysql)
  CONFIGURE_ARGS += --enable-nftables --enable-mysql
endif
ifeq ($(BUILD_VARIANT),nftpgsql)
  CONFIGURE_ARGS += --enable-nftables --enable-pgsql
  # Work around some brokenness in pkg-config test for libpq
  # These do not get checked until after pkconfig so do not prevent it.
  # --with-pgsql-include-path=$(STAGING_DIR)/usr/include --with-pgsql-lib-path=$(STAGING_DIR)/usr/lib
  # These do not work for some unknown reason
  #CONFIGURE_VARS+=PGSQL_CFLAGS="-I$(STAGING_DIR)/usr/include -L$(STAGING_DIR)/usr/lib"
  #CONFIGURE_VARS+=PGSQL_LIBS="-lpq"
  # Yes, PGSQL_CFLAGS adds nothing we do not already have but
  # must be present to prevent pkg-config
  export PGSQL_CFLAGS="-I$(STAGING_DIR)/usr/include -L$(STAGING_DIR)/usr/lib"
  export PGSQL_LIBS="-lpq"
endif

$(eval $(call BuildPackage,ebtables-dhcpsnooping-mysql-ap))
$(eval $(call BuildPackage,ebtables-dhcpsnooping-pgsql-ap))
$(eval $(call BuildPackage,nftables-dhcpsnooping-mysql-ap))
$(eval $(call BuildPackage,nftables-dhcpsnooping-pgsql-ap))

