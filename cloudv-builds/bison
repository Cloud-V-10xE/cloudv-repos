name: Build Bison (RISC-V)

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: [self-hosted, pioneer-1-admin]

    env:
      INSTALL_DIR: installed_binaries

    steps:
      - name: Clean Workspace
        run: |
          rm -rf *
      
      - name: Install Dependencies
        run: |
          set -eux
          export DEBIAN_FRONTEND=noninteractive
          sudo apt-get update
          sudo apt-get install -y \
            autoconf automake autopoint flex gperf \
            graphviz help2man texinfo git make \
            keychain openssh-client

      - name: Run system_info
        run: |
          echo '============================================================='
          echo 'CPU INFO'
          cat /proc/cpuinfo
          echo '============================================================='

          echo 'KERNEL INFO'
          uname -a
          echo '============================================================='

          echo 'GLIBC VERSION'
          ldd --version
          echo '============================================================='

          echo 'OS INFO'
          cat /etc/os-release
          echo '============================================================='

      - name: Clone Bison
        run: |
          set -eux
          mkdir -p $INSTALL_DIR
          git clone --branch master --single-branch --depth=1 https://github.com/akimd/bison.git

      - name: Bootstrap
        run: |
          set -eux
          cd bison
          git submodule update --init
          ./bootstrap
          ./autogen.sh

      - name: Configure
        run: |
          set -eux
          cd bison
          ./configure --prefix=$(readlink -f ../$INSTALL_DIR)

      - name: Build and Install
        run: |
          set -eux
          cd bison
          make -j$(nproc)
          make install

      - name: Check Version
        run: |
          set -eux
          ./$INSTALL_DIR/bin/bison --version
          ./$INSTALL_DIR/bin/yacc --version

      - name: Compress Binaries
        run: |
          set -eux
          FILENAME="bison_$(date -u +"%H%M%S_%d%m%Y").tar.gz"
          tar -czvf "$FILENAME" "$INSTALL_DIR"
          echo "FILENAME=$FILENAME" >> $GITHUB_ENV

      - name: Setup SSH Key
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.SSH_CLOUD_V_STORE_KEY }}" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan cloud-store >> ~/.ssh/known_hosts

      - name: Upload to Cloud
        run: |
          set -eux
          ssh cloud-store 'mkdir -p /var/www/nextcloud/data/admin/files/cloud-v-builds/bison'
          scp "$FILENAME" cloud-store:/var/www/nextcloud/data/admin/files/cloud-v-builds/bison/
          ssh cloud-store 'sudo -u www-data php /var/www/nextcloud/occ files:scan --path="admin/files"'
