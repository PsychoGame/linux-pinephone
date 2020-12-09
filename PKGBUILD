# AArch64 PinePhone/PineTab specific kernel
# Maintainer: Dan Johansen <strit@manjaro.org>
# Maintainer: Philip Müller <philm@manjaro.org>

pkgbase=linux-pinephone
_commit="db16c398a809ac303e7693f2f46ad20fad11d39a"
_srcname=linux-${_commit}
_kernelname=${pkgbase#linux}
_desc="Aarch64 PinePhone kernel"
pkgver=5.10rc
pkgrel=1
arch=('aarch64')
url="https://github.com/megous/linux"
license=('GPL2')
makedepends=('xmlto' 'docbook-xsl' 'kmod' 'inetutils' 'bc' 'git' 'uboot-tools' 'dtc')
options=('!strip')
source=("linux-$_commit.tar.gz::${url}/archive/${_commit}.tar.gz"
        'config'
        'linux.preset'
        '60-linux.hook'
        '90-linux.hook'
        'panic-led.patch'
        'enable-hdmi-output-pinetab.patch'
        'improve-brightness.patch'
        'enable-jack-detection-pinetab.patch'
        'pinetab-bluetooth.patch'
        'pinetab-accelerometer.patch'
        'camera-autofocus.patch'
        'media-ov5640-dont-break-when-firmware-for-autofocus-isnt-loaded.patch'
        'camera-added-bggr-bayer-mode.patch'
        '0002-Bluetooth-Fix-LL-PRivacy-BLE-device-fails-to-connect.patch'
        '0003-Bluetooth-Fix-attempting-to-set-RPA-timeout-when-unsupported.patch')
sha256sums=('68cbf1e3dda13252d3e6af370e6dbf8920a57b840f78e7ee8a7290c90e432f6f'
            '5cd8b89ab180cd3f4086051ccb62e951ea6cb5d8559d3bbc4e18306cb07c8c2c'
            'f704a0e790a310f88b76bf5ae7200ef6f47fd6c68c0d2447de0f121cfc93c5ad'
            'ae2e95db94ef7176207c690224169594d49445e04249d2499e9d2fbc117a0b21'
            '71df1b18a3885b151a3b9d926a91936da2acc90d5e27f1ad326745779cd3759d'
            '27717d53ecf945c45e03a83f1e82f82d87d5785968beccbec977f84fc9e07ea7'
            'a3b98f1c514dfbc563691e502ceeb05f734aadb7ea3af0e0d2866cb515548529'
            '870cf28731738129d653bfbfbe1d1928ccee1dfb38734cc9e74aa45889a58802'
            '1ef1c44720798f5e7dcd57ec066e11cb0d4c4db673efcb74b2239534add9564c'
            'dc4048106a515b3deb43c9de47674d0c99028336723e71d5df2a3897352df524'
            '0e6453bf258c34349e5cc76811d804392aaa1ef9230c343719879682aaff7515'
            '6270614e74fddfb272ec079379c9a0dfcc1204e2179505ed562672b75ee26249'
            '94fa9a857169538c795a327f0b1d540e236cc89ec5a152b8760e157495a6d3fc'
            'dfb340a8e8d47336a93c3183e1978c21a14c68c3d4aa9ac48e39eb9b5d8444d7'
            '3c1c63194dc808b63166646d71c9fc2e690605178527bdd5528084613990207a'
            'e216346f7c7761a53b76dea0638898e52aac9e4527a64893c9dfa9936b3c5a0d')

prepare() {
  cd "${srcdir}/${_srcname}"

  local src
  for src in "${source[@]}"; do
      src="${src%%::*}"
      src="${src##*/}"
      [[ $src = *.patch ]] || continue
      msg2 "Applying patch: $src..."
      patch -Np1 < "../$src"
  done

  cat "${srcdir}/config" > ./.config

  # add pkgrel to extraversion
  sed -ri "s|^(EXTRAVERSION =)(.*)|\1 \2-${pkgrel}|" Makefile

  # don't run depmod on 'make install'. We'll do this ourselves in packaging
  sed -i '2iexit 0' scripts/depmod.sh
  
  cd "${srcdir}/${_srcname}"

  # get kernel version
  make prepare

  # load configuration
  # Configure the kernel. Replace the line below with one of your choice.
  #make menuconfig # CLI menu for configuration
  #make nconfig # new CLI menu for configuration
  #make xconfig # X-based configuration
  #make oldconfig # using old config from previous kernel version
  # ... or manually edit .config
  #make pine64_defconfig

  # Copy back our configuration (use with new kernel version)
  #cp ./.config /var/tmp/${pkgbase}.config

  ####################
  # stop here
  # this is useful to configure the kernel
  #msg "Stopping build"
  #return 1
  ####################

  #yes "" | make config  
}

build() {
  cd "${srcdir}/${_srcname}"

  # build!
  unset LDFLAGS
  make ${MAKEFLAGS} Image Image.gz modules
  make ${MAKEFLAGS} DTC_FLAGS="-@" dtbs
}

_package() {
  pkgdesc="The Linux Kernel and modules - ${_desc}"
  depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7'
           'rtl8723bt-firmware-megi' 'ov5640-firmware')
  optdepends=('crda: to set the correct wireless channels of your country')
  provides=('kernel26' "linux=${pkgver}")
  replaces=('linux-armv8-rc')
  conflicts=('linux')
  backup=("etc/mkinitcpio.d/${pkgbase}.preset")
  install=${pkgname}.install

  cd "${srcdir}/${_srcname}"

  KARCH=arm64

  # get kernel version
  _kernver="$(make kernelrelease)"
  _basekernel=${_kernver%%-*}
  _basekernel=${_basekernel%.*}

  mkdir -p "${pkgdir}"/{boot,usr/lib/modules}
  make INSTALL_MOD_PATH="${pkgdir}/usr" modules_install
  make INSTALL_DTBS_PATH="${pkgdir}/boot/dtbs" dtbs_install
  cp arch/$KARCH/boot/Image{,.gz} "${pkgdir}/boot"

  # make room for external modules
  local _extramodules="extramodules-${_basekernel}${_kernelname}"
  ln -s "../${_extramodules}" "${pkgdir}/usr/lib/modules/${_kernver}/extramodules"

  # add real version for building modules and running depmod from hook
  echo "${_kernver}" |
    install -Dm644 /dev/stdin "${pkgdir}/usr/lib/modules/${_extramodules}/version"

  # remove build and source links
  rm "${pkgdir}"/usr/lib/modules/${_kernver}/{source,build}

  # now we call depmod...
  depmod -b "${pkgdir}/usr" -F System.map "${_kernver}"

  # add vmlinux
  install -Dt "${pkgdir}/usr/lib/modules/${_kernver}/build" -m644 vmlinux

  # sed expression for following substitutions
  local _subst="
    s|%PKGBASE%|${pkgbase}|g
    s|%KERNVER%|${_kernver}|g
    s|%EXTRAMODULES%|${_extramodules}|g
  "

  # install mkinitcpio preset file
  sed "${_subst}" ../linux.preset |
    install -Dm644 /dev/stdin "${pkgdir}/etc/mkinitcpio.d/${pkgbase}.preset"

  # install pacman hooks
  sed "${_subst}" ../60-linux.hook |
    install -Dm644 /dev/stdin "${pkgdir}/usr/share/libalpm/hooks/60-${pkgbase}.hook"
  sed "${_subst}" ../90-linux.hook |
    install -Dm644 /dev/stdin "${pkgdir}/usr/share/libalpm/hooks/90-${pkgbase}.hook"
}

_package-headers() {
  pkgdesc="Header files and scripts for building modules for linux kernel - ${_desc}"
  provides=("linux-headers=${pkgver}")
  conflicts=('linux-headers')
  cd "${srcdir}/${_srcname}"
  local _builddir="${pkgdir}/usr/lib/modules/${_kernver}/build"

  install -Dt "${_builddir}" -m644 Makefile .config Module.symvers
  install -Dt "${_builddir}/kernel" -m644 kernel/Makefile

  mkdir "${_builddir}/.tmp_versions"

  cp -t "${_builddir}" -a include scripts

  install -Dt "${_builddir}/arch/${KARCH}" -m644 arch/${KARCH}/Makefile
  install -Dt "${_builddir}/arch/${KARCH}/kernel" -m644 arch/${KARCH}/kernel/asm-offsets.s arch/$KARCH/kernel/module.lds

  cp -t "${_builddir}/arch/${KARCH}" -a arch/${KARCH}/include
  mkdir -p "${_builddir}/arch/arm"
  cp -t "${_builddir}/arch/arm" -a arch/arm/include

  install -Dt "${_builddir}/drivers/md" -m644 drivers/md/*.h
  install -Dt "${_builddir}/net/mac80211" -m644 net/mac80211/*.h

  # http://bugs.archlinux.org/task/13146
  install -Dt "${_builddir}/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # http://bugs.archlinux.org/task/20402
  install -Dt "${_builddir}/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "${_builddir}/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "${_builddir}/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # add xfs and shmem for aufs building
  mkdir -p "${_builddir}"/{fs/xfs,mm}

  # copy in Kconfig files
  find . -name Kconfig\* -exec install -Dm644 {} "${_builddir}/{}" \;

  # remove unneeded architectures
  local _arch
  for _arch in "${_builddir}"/arch/*/; do
    [[ ${_arch} == */${KARCH}/ || ${_arch} == */arm/ ]] && continue
    rm -r "${_arch}"
  done

  # remove files already in linux-docs package
  rm -r "${_builddir}/Documentation"

  # remove now broken symlinks
  find -L "${_builddir}" -type l -printf 'Removing %P\n' -delete

  # Fix permissions
  chmod -R u=rwX,go=rX "${_builddir}"

  # strip scripts directory
  local _binary _strip
  while read -rd '' _binary; do
    case "$(file -bi "${_binary}")" in
      *application/x-sharedlib*)  _strip="${STRIP_SHARED}"   ;; # Libraries (.so)
      *application/x-archive*)    _strip="${STRIP_STATIC}"   ;; # Libraries (.a)
      *application/x-executable*) _strip="${STRIP_BINARIES}" ;; # Binaries
      *) continue ;;
    esac
    /usr/bin/strip ${_strip} "${_binary}"
  done < <(find "${_builddir}/scripts" -type f -perm -u+w -print0 2>/dev/null)
}

pkgname=("${pkgbase}" "${pkgbase}-headers")
for _p in ${pkgname[@]}; do
  eval "package_${_p}() {
    _package${_p#${pkgbase}}
  }"
done
