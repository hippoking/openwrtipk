# openwrtipk
customized ipk packages for openwrt CC 15.05.1, suitable for K2P router.

Environment setting up:
cd ~
apt-get update
apt-get upgrade
sudo apt-get install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils
sudo apt-get install ccache

Downloading SDK:
wget http://archive.openwrt.org/chaos_calmer/15.05.1/ramips/mt7621/OpenWrt-SDK-15.05.1-ramips-mt7621_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-x86_64.tar.bz2
tar xjf http://archive.openwrt.org/chaos_calmer/15.05.1/ramips/mt7621/OpenWrt-SDK-15.05.1-ramips-mt7621_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-x86_64.tar.bz2
mv OpenWrt-SDK-15.05.1-ramips-mt7621_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-x86_64 cc
cd cc

Update feeds:
./script/feeds update


-----------------------------------------------------------------------------------
Recompile libopenssl to use more encryption methods like camellia
#get openssl directory from openwrt cc 15.05.1 or openwrt 18.06.1 or latest openwrt repo package/libs and copy this directory to ~/cc/package/
#edit Makefile to remove methods from NO_CIPHERS
make menuconfig
make V=s

#after several miniutes, you should be able to see recompiled libopenssl and openssl-utils ipk under ~/cc/bin/ramips/package/base directory
#you can upgrade libopenssl and openssl-utils directly in openwrt console:
opkg install libopenssl.ipk
opkg install openssl-utils.ipk


-----------------------------------------------------------------------------------
Compile official shadowsocks from Openwrt github:
cd ~
git clone https://github.com/openwrt/packages.git
cd packages/net
cp shadowsocks-libev ~/cc/package/ -R
cd ~/cc
## shadowsocks needs some library and depends
./script/feeds install libopenssl libpolarssl
git clone https://github.com/shadowsocks/openwrt-feeds.git package/feeds
## setting up make menu
make menuconfig
## start compiling
make V=s

After compiling, you will get below ipk files:
shadowsocks-libev-config
shadowsocks-libev-ss-local
shadowsocks-libev-ss-redir
shadowsocks-libev-ss-rules
shadowsocks-libev-ss-server
shadowsocks-libev-ss-tunnel

------------------------------------------------------------------------------------
Lean's apps including SS-Pro and SS-Plus
cd ~
git clone https://github.com/coolsnowwolf/lede.git
cd lede/package/lean
cp frpc ipset-lists luci-app-frpc luci-app-ssr-plus luci-app-ssr-pro pdnsd-alt polarssl shadowsocksR-libev-full ~/cc/package/ -R
#install necessary libs
./scripts/feeds install libpcre
./scripts/feeds install libopenssl
# you can edit libopenssl Makefile(~/cc/package/feeds/base/openssl) to add more encryption methods for your sake

make menuconfig
make V=s




