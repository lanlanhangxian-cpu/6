name: Build K10x ReSukiSU+SUSFS Kernel
on: workflow_dispatch

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 源码
        uses: actions/checkout@v3

      - name: 安装依赖
        run: |
          sudo apt-get update
          sudo apt-get install -y gcc-aarch64-linux-gnu libncurses-dev flex bison bc

      - name: 编译内核
        run: |
          export ARCH=arm64
          export CROSS_COMPILE=aarch64-linux-gnu-
          make oscar_user_defconfig   # 替换为实际 defconfig
          make -j$(nproc)

      - name: 打包 AnyKernel3
        run: |
          git clone https://github.com/osm0sis/AnyKernel3
          cp arch/arm64/boot/Image.gz-dtb AnyKernel3/
          # 若准备了官方模块，复制进去（可选）
          if [ -d "official_modules" ]; then
            cp -r official_modules/* AnyKernel3/modules/
          fi
          cd AnyKernel3
          sed -i 's/device.name1=.*/device.name1=oscar/' anykernel.sh
          zip -r9 ../K10x_ReSukiSU_SUSFS-v2.0.0.zip ./*

      - name: 上传刷机包
        uses: actions/upload-artifact@v3
        with:
          name: K10x_ReSukiSU_SUSFS
          path: K10x_ReSukiSU_SUSFS-v2.0.0.zip
