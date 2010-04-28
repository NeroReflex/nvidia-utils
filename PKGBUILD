# Maintainer: Thomas Baechler <thomas@archlinux.org>
# Contributor: James Rayner <iphitus@gmail.com>

pkgname=nvidia-utils
pkgver=195.36.24
pkgrel=2
pkgdesc="NVIDIA drivers utilities and libraries."
arch=('i686' 'x86_64')
[ "$CARCH" = "i686"   ] && ARCH=x86
[ "$CARCH" = "x86_64" ] && ARCH=x86_64
url="http://www.nvidia.com/"
depends=('xorg-server')
optdepends=('gtk2: nvidia-settings' 'pkgconfig: nvidia-xconfig')
conflicts=('libgl')
provides=('libgl')
license=('custom')
install=nvidia.install
options=(!strip)
source=("http://download.nvidia.com/XFree86/Linux-${ARCH}/${pkgver}/NVIDIA-Linux-${ARCH}-${pkgver}-pkg0.run"
        20-nvidia.conf)
md5sums=('97d3e5f69707092aa643fb901bb94003'
         'adbefbc0e570b3bd2b918ffd74443dde')
[ "$CARCH" = "x86_64" ] && md5sums[0]='9d58114ed8c89cd66fbaccad0c997ee8'

build() {
	cd $srcdir
	sh NVIDIA-Linux-${ARCH}-${pkgver}-pkg0.run --extract-only
}

package() {
	cd $srcdir/NVIDIA-Linux-${ARCH}-${pkgver}-pkg0/usr/

	mkdir -p $pkgdir/usr/{lib,bin,share/applications,share/pixmaps,share/man/man1}
	mkdir -p $pkgdir/usr/lib/xorg/modules/{extensions,drivers}
	mkdir -p $pkgdir/usr/lib/vdpau
	mkdir -p $pkgdir/usr/share/licenses/nvidia
	mkdir -p $pkgdir/usr/include/cuda

	install -m644 include/cuda/cuda*.h $pkgdir/usr/include/cuda/

	install lib/{libGLcore,libGL,libnvidia-cfg,libcuda,tls/libnvidia-tls}.so.${pkgver} $pkgdir/usr/lib/ || return 1
	install -m755 lib/vdpau/libvdpau_nvidia.so.${pkgver} "${pkgdir}/usr/lib/vdpau/" || return 1
	ln -sf libvdpau_nvidia.so.${pkgver} "${pkgdir}/usr/lib/vdpau/libvdpau_nvidia.so.1"

	install -m644 share/man/man1/* $pkgdir/usr/share/man/man1/ || return 1
	rm $pkgdir/usr/share/man/man1/nvidia-installer.1.gz || return 1
	install X11R6/lib/libXv* $pkgdir/usr/lib/ || return 1
	install -m644 share/applications/nvidia-settings.desktop $pkgdir/usr/share/applications/ || return 1
	# fix nvidia .desktop file
	sed -e 's:__UTILS_PATH__:/usr/bin:' -e 's:__PIXMAP_PATH__:/usr/share/pixmaps:' -i $pkgdir/usr/share/applications/nvidia-settings.desktop
	install -m644 share/pixmaps/nvidia-settings.png $pkgdir/usr/share/pixmaps/ || return 1
	install X11R6/lib/modules/drivers/nvidia_drv.so $pkgdir/usr/lib/xorg/modules/drivers || return 1
	install X11R6/lib/modules/extensions/libglx.so.$pkgver $pkgdir/usr/lib/xorg/modules/extensions || return 1
	install -m755 bin/nvidia-{settings,xconfig,bug-report.sh} $pkgdir/usr/bin/ || return 1
	cd $pkgdir/usr/lib/
	ln -s libGL.so.$pkgver libGL.so || return 1
	ln -s libGL.so.$pkgver libGL.so.1 || return 1
	ln -s libGLcore.so.$pkgver libGLcore.so.1 || return 1
	ln -s libnvidia-cfg.so.$pkgver libnvidia-cfg.so.1 || return 1
	ln -s libnvidia-tls.so.$pkgver libnvidia-tls.so.1 || return 1
	ln -s libcuda.so.$pkgver libcuda.so.1 || return 1
	ln -s libcuda.so.$pkgver libcuda.so || return 1
	ln -s libXvMCNVIDIA.so.$pkgver libXvMCNVIDIA_dynamic.so.1 || return 1

	cd $pkgdir/usr/lib/xorg/modules/extensions
	ln -s libglx.so.$pkgver libglx.so || return 1

	install -m644 $srcdir/NVIDIA-Linux-${ARCH}-${pkgver}-pkg0/LICENSE $pkgdir/usr/share/licenses/nvidia/ || return 1
	ln -s nvidia $startdir/pkg/usr/share/licenses/nvidia-utils || return 1

	install -D -m644 $srcdir/NVIDIA-Linux-${ARCH}-${pkgver}-pkg0/usr/share/doc/README.txt $pkgdir/usr/share/doc/nvidia/README || return 1

	find $pkgdir/usr -type d -exec chmod 755 {} \;
	chmod 644 $pkgdir/usr/lib/libXvMCNVIDIA.a || return 1
	
	# Install xorg.conf.d file for nvidia autodetection in xorg.conf-less configurations
	install -D -m644 $srcdir/20-nvidia.conf $pkgdir/etc/xorg.conf.d/20-nvidia.conf || return 1
}
