name: Build Go (Template)

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: self-hosted
    # If your runner has labels:
    # runs-on: [self-hosted, pioneer-1-admin]

    steps:
      - name: Clean Workspace
        run: |
          rm -rf *

      - name: Install Dependencies
        run: |
          set -eux
          export DEBIAN_FRONTEND=noninteractive
          sudo apt-get update
          sudo apt-get install -y golang gcc

      - name: Run system_info
        run: |
          echo '============================================================='
          echo '                       CPU INFO START                        '
          echo '============================================================='
          cat /proc/cpuinfo
          echo '============================================================='
          echo '                       CPU INFO END                          '
          echo '============================================================='

          echo '============================================================='
          echo '                       Kernel Info Start                        '
          echo '============================================================='
          uname -a
          echo '============================================================='
          echo '                       Kernel Info End                          '
          echo '============================================================='

          echo '============================================================='
          echo '                       Glibc Version Start                        '
          echo '============================================================='
          ldd --version
          echo '============================================================='
          echo '                       Glibc Version End                          '
          echo '============================================================='

          echo '============================================================='
          echo '                       OS Info Start                        '
          echo '============================================================='
          cat /etc/os-release
          echo '============================================================='
          echo '                       OS Info End                        '
          echo '============================================================='

      - name: Clone Go Source
        run: |
          set -eux
          git clone --single-branch --depth=1 https://go.googlesource.com/go

      - name: Build Go
        run: |
          set -eux
          cd go/src
          ./all.bash

      - name: Check Go Version
        run: |
          set -eux
          ./go/bin/go version

      - name: Compress Binaries
        run: |
          set -eux
          FILENAME="go_$(date -u +"%H%M%S_%d%m%Y").tar.gz"
          echo "FILENAME=$FILENAME" >> $GITHUB_ENV
          tar -cvf "$FILENAME" ./go

      - name: Setup SSH
        uses: webfactory/ssh-agent@v0.9.0
        with:
          ssh-private-key: ${{ secrets.SSH_CLOUD_V_STORE_ID }}

      - name: Upload to Cloud
        run: |
          set -eux
          ssh cloud-store 'mkdir -p /var/www/nextcloud/data/admin/files/cloud-v-builds/go'
          ssh cloud-store 'rm /var/www/nextcloud/data/admin/files/cloud-v-builds/go/*' || true
          scp "$FILENAME" cloud-store:/var/www/nextcloud/data/admin/files/cloud-v-builds/go/
          ssh cloud-store 'sudo -u www-data php /var/www/nextcloud/occ files:scan --path="admin/files"'
