# Maintainer: Edmunt Pienkowsky <roed@onet.eu>

pkgname=grafana-image-renderer
pkgver=1.0.5
pkgrel=1
pkgdesc='A Grafana web service that handles rendering of panels & dashboards to PNGs using headless Chrome'
url='http://grafana.com/docs/administration/image_rendering'
arch=('any')
license=('Apache')
depends=('nodejs' 'chromium')
optdepends=('nginx' 'grafana')
makedepends=('yarn')
source=(
    "http://github.com/RoEdAl/${pkgname}/archive/v${pkgver}-wp${pkgrel}.tar.gz"
    'grafana-image-renderer.service'
    'nginx-location.conf'
    'grafana-rendering.ini'
)
sha256sums=('0d985af32767efc1f716b76647fa4fa21d230ae0c833c5f64c7b62355582b44d'
            '09a5bb7f4101a4bef32059f3306581e1a2eb15b75746151cc3d14a4ffab92404'
            'b797d5b354c96e89c861c48a2a5ac36148226f266de9598c9ed78a7546dddcc9'
            '4b71184a1068e0a59c37fc248724e4663b5b9aa4110a789b9a7750b95d38ccf8')

build() {
  msg2 'Cache setup'
  local yarn_cache_folder="$srcdir/tmp/yarn"
  local npm_config_devdir="$srcdir/tmp/gyp"
  local npm_config_prefix="$srcdir/tmp/npm"
  local NODETMP="$srcdir/tmp/node"
  mkdir -p "$yarn_cache_folder"
  mkdir -p "$npm_config_devdir"
  mkdir -p "$npm_config_prefix"
  mkdir -p "$NODETMP"

  cd ${pkgname}-${pkgver}-wp${pkgrel}
  msg2 'Building'
  env \
    npm_config_devdir="$npm_config_devdir" \
    npm_config_prefix="$npm_config_prefix" \
    YARN_CACHE_FOLDER="$yarn_cache_folder" \
    TMPDIR="$NODETMP" \
    yarn install --pure-lockfile --no-progress --network-concurrency=1 --network-timeout=60000
  env \
    npm_config_devdir="$npm_config_devdir" \
    npm_config_prefix="$npm_config_prefix" \
    TMPDIR="$NODETMP" \
    yarn run build-server
  env \
    npm_config_devdir="$npm_config_devdir" \
    npm_config_prefix="$npm_config_prefix" \
    TMPDIR="$NODETMP" \
    yarn run node-modules
}

package() {
  install -dm755 $pkgdir/usr/lib/$pkgname
  install -dm755 $pkgdir/usr/share/$pkgname
  install -dm755 $pkgdir/usr/lib/systemd/system

  cd ${pkgname}-${pkgver}-wp${pkgrel}
  cp -r dist $pkgdir/usr/lib/$pkgname
  cp -r proto $pkgdir/usr/lib/$pkgname
  cp -r node_modules_production $pkgdir/usr/lib/$pkgname/node_modules

  install -Dm644 $srcdir/grafana-image-renderer.service $pkgdir/usr/lib/systemd/system
  install -Dm644 $srcdir/nginx-location.conf $pkgdir/usr/share/$pkgname
  install -Dm644 $srcdir/grafana-rendering.ini $pkgdir/usr/share/$pkgname
}
