name: Build GNU Make

on:
  workflow_dispatch:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: self-hosted
    # If your runner has labels like: pioneer-1-admin
    # use:
    # runs-on: [self-hosted, pioneer-1-admin]

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Clean Workspace
        run: |
          echo "Cleaning workspace..."
          rm -rf *

      - name: Install Dependencies
        run: |
          set -x
          sudo apt-get update
          sudo apt-get install -y autoconf autopoint wget git texinfo

      - name: Run System Info
        run: |
          echo "================ CPU INFO ================"
          cat /proc/cpuinfo
          echo "================ KERNEL INFO ================"
          uname -a
          echo "================ GLIBC VERSION ================"
          ldd --version
          echo "================ OS INFO ================"
          cat /etc/os-release

      - name: Clone GNU Make
        run: |
          mkdir installed_binaries
          git clone --branch master --single-branch --depth=1 https://git.savannah.gnu.org/git/make.git

      - name: Bootstrap and Configure
        run: |
          set -x
          cd make
          ./bootstrap
          ./configure --prefix=$(readlink -f ../installed_binaries)

      - name: Build and Install
        run: |
          set -x
          cd make
          make -j$(nproc)
          make install

      - name: Check Version
        run: |
          set -x
          ./installed_binaries/bin/make --version

      - name: Compress Binaries
        id: package
        run: |
          FILENAME="make_$(date -u +"%H%M%S_%d%m%Y").tar.gz"
          echo "FILENAME=$FILENAME" >> $GITHUB_ENV
          tar -czvf $FILENAME installed_binaries

      - name: Setup SSH
        uses: webfactory/ssh-agent@v0.9.0
        with:
          ssh-private-key: ${{ secrets.SSH_CLOUD_V_STORE_KEY }}

      - name: Upload to Cloud Server
        run: |
          set -x
          ssh -o StrictHostKeyChecking=no cloud-store 'mkdir -p /var/www/nextcloud/data/admin/files/cloud-v-builds/make'
          ssh cloud-store 'rm -f /var/www/nextcloud/data/admin/files/cloud-v-builds/make/*'
          scp $FILENAME cloud-store:/var/www/nextcloud/data/admin/files/cloud-v-builds/make/
          ssh cloud-store 'sudo -u www-data php /var/www/nextcloud/occ files:scan --path="admin/files"'
