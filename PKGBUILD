# AArch64 PinePhone/PineTab specific kernel
# Maintainer: Dan Johansen <strit@manjaro.org>
# Maintainer: Philip Müller <philm@manjaro.org>

pkgbase=linux-pinephone
_commit="8504203b708b17381ffdf0941656f4617ef2bc87"
_srcname=linux-${_commit}
_kernelname=${pkgbase#linux}
_desc="Aarch64 PinePhone kernel"
pkgver=5.9.6
pkgrel=2
arch=('aarch64')
url="https://github.com/megous/linux"
license=('GPL2')
makedepends=('xmlto' 'docbook-xsl' 'kmod' 'inetutils' 'bc' 'git' 'uboot-tools' 'dtc')
options=('!strip')
source=("linux-$_commit.tar.gz::${url}/archive/${_commit}.tar.gz"
        'https://cdn.kernel.org/pub/linux/kernel/v5.x/incr/patch-5.9.3-4.xz'
        'https://cdn.kernel.org/pub/linux/kernel/v5.x/incr/patch-5.9.4-5.xz'
        'https://cdn.kernel.org/pub/linux/kernel/v5.x/incr/patch-5.9.5-6.xz'
        'config'
        'linux.preset'
        '60-linux.hook'
        '90-linux.hook'
        'wifi-power-saving.patch'
        'enable-jack-detection-pinetab.patch'
        'panic-led.patch'
        'enable-hdmi-output-pinetab.patch'
        'improve-brightness.patch'
        '0001-revert-fbcon-remove-now-unusued-softback_lines-cursor-argument.patch'
        '0002-revert-fbcon-remove-soft-scrollback-code.patch'
        '0001-bootsplash.patch'
        '0002-bootsplash.patch'
        '0003-bootsplash.patch'
        '0004-bootsplash.patch'
        '0005-bootsplash.patch'
        '0006-bootsplash.patch'
        '0007-bootsplash.patch'
        '0008-bootsplash.patch'
        '0009-bootsplash.patch'
        '0010-bootsplash.patch'
        '0011-bootsplash.patch'
        '0012-bootsplash.patch')
sha256sums=('dd82a24c9185bb5152e90912b3cea6881f61cd680284230056584e20e3db2cc0'
            '9012eaf4535e39d43536b55908affe3d7e7246d239c73e7047b30cdff48f281a'
            '4e40a9a0ad93ebeba99ea54ab4b8504a412de478b5fa368c454c35ca440a29a6'
            'db9b798ac73ed93bc1f25e5c44361515b676fa7f5d473e0722a5cff3d90a3a1b'
            '5cd8b89ab180cd3f4086051ccb62e951ea6cb5d8559d3bbc4e18306cb07c8c2c'
            'f704a0e790a310f88b76bf5ae7200ef6f47fd6c68c0d2447de0f121cfc93c5ad'
            'ae2e95db94ef7176207c690224169594d49445e04249d2499e9d2fbc117a0b21'
            '71df1b18a3885b151a3b9d926a91936da2acc90d5e27f1ad326745779cd3759d'
            'bb7819e9d0fd615ecc6c95ece74e5566a86e86c8711194af74bdad426e15c859'
            '1ef1c44720798f5e7dcd57ec066e11cb0d4c4db673efcb74b2239534add9564c'
            '27717d53ecf945c45e03a83f1e82f82d87d5785968beccbec977f84fc9e07ea7'
            'a3b98f1c514dfbc563691e502ceeb05f734aadb7ea3af0e0d2866cb515548529'
            '870cf28731738129d653bfbfbe1d1928ccee1dfb38734cc9e74aa45889a58802'
            'ddf1e7fc55cc6fe81ecfcac84112e573ca95713c027bc84d69cf880812fd6ff3'
            '37a221c12b40122167b0a30b5a9f2fc99e2aeb94e4db58a719c2b30171c5aeb5'
            '59202940d4f12bad23c194a530edc900e066866c9945e39748484a6545af96de'
            'e096b127a5208f56d368d2cb938933454d7200d70c86b763aa22c38e0ddb8717'
            '8c1c880f2caa9c7ae43281a35410203887ea8eae750fe8d360d0c8bf80fcc6e0'
            '1144d51e5eb980fceeec16004f3645ed04a60fac9e0c7cf88a15c5c1e7a4b89e'
            'dd4b69def2efacf4a6c442202ad5cb93d492c03886d7c61de87696e5a83e2846'
            '028b07f0c954f70ca37237b62e04103e81f7c658bb8bd65d7d3c2ace301297dc'
            'c8b0cb231659d33c3cfaed4b1f8d7c8305ab170bdd4c77fce85270d7b6a68000'
            '8dbb5ab3cb99e48d97d4e2f2e3df5d0de66f3721b4f7fd94a708089f53245c77'
            'a7aefeacf22c600fafd9e040a985a913643095db7272c296b77a0a651c6a140a'
            'e9f22cbb542591087d2d66dc6dc912b1434330ba3cd13d2df741d869a2c31e89'
            '27471eee564ca3149dd271b0817719b5565a9594dc4d884fe3dc51a5f03832bc'
            '60e295601e4fb33d9bf65f198c54c7eb07c0d1e91e2ad1e0dd6cd6e142cb266d')

prepare() {
  cd "${srcdir}/${_srcname}"

  msg2 "Applying patch: 5.9.3-4"
  patch -Np1 < ../patch-5.9.3-4
  msg2 "Applying patch: 5.9.4-5"
  patch -Np1 < ../patch-5.9.4-5
  msg2 "Applying patch: 5.9.5-6"
  patch -Np1 < ../patch-5.9.5-6

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
  depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7')
  optdepends=('crda: to set the correct wireless channels of your country'
              'ov5640-firmware: to support autofocus')
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
