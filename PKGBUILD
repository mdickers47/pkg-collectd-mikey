# Maintainer: lilydjwg <lilydjwg@gmail.com>
# Contributor: Gaetan Bisson <bisson@archlinux.org>
# Contributor: Gerhard Brauer <gerhard.brauer@web.de>
# Contributor/forker: Mikey Dickerson <pomonamikey@gmail.com>

pkgname=collectd-mikey
pkgver=5.12.0
pkgrel=13
pkgdesc='Daemon which collects system performance statistics periodically'
url='https://collectd.org/'
arch=('x86_64' 'aarch64')
license=('GPL')

optdepends=('curl: apache, ascent, curl, nginx, and write_http plugins'
            'libdbi: dbi plugin'
            'libesmtp: notify_email plugin'
            'libmemcached: memcachec plugin'
            'mariadb-libs: mysql plugin'
            'systemd-libs: UdevNameAttr option'
            'iproute2: netlink plugin'
            'net-snmp: snmp plugin'
            'libnotify: notify_desktop plugin'
            'openipmi: ipmi plugin'
            'liboping: ping plugin'
            'libpcap: dns plugin'
            'perl: perl plugin'
            'postgresql-libs: postgresql plugin'
            'python: python plugin'
            'rrdtool: rrdtool and rrdcached plugins'
            'lm_sensors: lm_sensors and sensors plugins'
            'libvirt: libvirt plugin'
            'libxml2: ascent and libvirt plugins'
            'yajl: curl_json plugin'
            'libatasmart: smart plugin'
            'lvm2: lvm plugin'
            'protobuf-c: write_riemann plugin'
            'mosquitto: MQTT plugin'
            'libmicrohttpd: prometheus plugin'
            'librabbitmq-c: amqp plugin'
            'nut: nut plugin')

makedepends=(${optdepends[@]%:*} 'automake')
depends=('libltdl' 'iptables' 'libnsl')
provides=('collectd')
conflicts=('collectd')

source=("https://storage.googleapis.com/collectd-tarballs/collectd-${pkgver}.tar.bz2"
        'service'
        'https://github.com/collectd/collectd/commit/623e95394e0e62e7f9ced2104b786d21e9c0bf53.patch'
        'https://github.com/collectd/collectd/commit/d409ffa2a64cac3fc2abe2bb86ec3a80cb34d0a6.patch'
        'https://global.download.synology.com/download/Document/Software/DeveloperGuide/Firmware/DSM/All/enu/Synology_MIB_File.zip'
        'https://raw.githubusercontent.com/vitkabele/dsm-snmp-collectd/master/dsm-snmp.conf'
        'collectd.conf'
        'graphite.conf')
sha256sums=('5bae043042c19c31f77eb8464e56a01a5454e0b39fa07cf7ad0f1bfc9c3a09d6'
            '83957b0b2cc7fa05a4d5f22e6c913ae2b0a4d7821f7b4e2d2e763054cc8c6c21'
            '777544cbf803af4d08ea228b29619f8f6e7a4777a85e0fb30693d8240db7246b'
	    'ce30bf4a6cf1565d45656d5cd7e668542bada3f3cf6f4198df4eaf8fdcd97030'
            '13c29b6c13a70495f02e054dff00ebf9498be54729157258eb72e06530edd825'
            'cf510b1010cae65e9de810ec8ff10d624009658b75b1664ad70a34b552fac784'
            'db0801a73e3f282e2ae13bb1993639285df789abd5b074a2f51ffc169a2ec127'
            'b5908e6b793a6f8bf2e20eda6d59324dcd4b52d53d1781e9b75973df53638ba1')

backup=('etc/collectd.conf')

prepare() {
	cd collectd-${pkgver}
	sed -i '/sys\/mount.h/d' src/utils/mount/mount.h
        patch -Np1 < ../623e95394e0e62e7f9ced2104b786d21e9c0bf53.patch
	patch -Np1 < ../d409ffa2a64cac3fc2abe2bb86ec3a80cb34d0a6.patch
}

build() {
	cd collectd-${pkgver}
	autoreconf -f -i
	./configure \
		--prefix=/usr \
		--sysconfdir=/etc \
		--localstatedir=/var \
		--sbindir=/usr/bin \
		--disable-werror \
		--with-perl-bindings='INSTALLDIRS=vendor' \
	        --without-libgcrypt
	make all
}

package() {
	cd collectd-${pkgver}
	make DESTDIR="${pkgdir}" install
	rmdir "${pkgdir}/var/run" # FS#30201
	install -Dm644 ../service "${pkgdir}"/usr/lib/systemd/system/collectd.service
	install -Dm644 contrib/collectd2html.pl "${pkgdir}"/usr/share/collectd/collectd2html.pl
	cd ..
	install -dm755 ${pkgdir}/etc/collectd.d
        install -m644 collectd.conf  ${pkgdir}/etc/collectd.conf
        install -m644 dsm-snmp.conf graphite.conf ${pkgdir}/etc/collectd.d
        cd Synology_MIB_File
        install -dm755 ${pkgdir}/usr/share/snmp/mibs
        install -m644 *.txt ${pkgdir}/usr/share/snmp/mibs
}
