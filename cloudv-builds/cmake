name: Build CMake (RISC-V)

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
      REMOTE_PATH: /var/www/nextcloud/data/admin/files/cloud-v-builds/cmake

    steps:
      - name: Clean Workspace
        run: |
          rm -rf *

      - name: Install Dependencies
        run: |
          set -eux
          export DEBIAN_FRONTEND=noninteractive
          sudo apt update
          sudo apt-get install -y \
            cmake make git \
            build-essential \
            openssh-client

      - name: Run system_info
        run: |
          echo "================ CPU INFO ================"
          cat /proc/cpuinfo

          echo "================ KERNEL INFO ================"
          uname -a

          echo "================ GLIBC VERSION ================"
          ldd --version

          echo "================ OS INFO ================"
          cat /etc/os-release

      - name: Clone CMake
        run: |
          set -eux
          mkdir -p $INSTALL_DIR
          git clone --branch master --single-branch --depth=1 \
            https://gitlab.kitware.com/cmake/cmake.git

      - name: Bootstrap
        run: |
          set -eux
          cd cmake
          ./bootstrap --prefix=$(readlink -f ../$INSTALL_DIR)

      - name: Build and Install
        run: |
          set -eux
          cd cmake
          make -j$(nproc)
          make install

      - name: Check Version
        run: |
          set -eux
          ./$INSTALL_DIR/bin/cmake --version

      - name: Compress Binaries
        run: |
          set -eux
          FILENAME="cmake_$(date -u +"%H%M%S_%d%m%Y").tar.gz"
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
          ssh cloud-store "mkdir -p $REMOTE_PATH"
          ssh cloud-store "rm -f $REMOTE_PATH/*"
          scp "$FILENAME" cloud-store:$REMOTE_PATH/
          ssh cloud-store 'sudo -u www-data php /var/www/nextcloud/occ files:scan --path="admin/files"'
