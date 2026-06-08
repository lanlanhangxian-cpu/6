name: Build K10x ResukiSU + SUSFS Kernel
on: [push, workflow_dispatch]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 内核源码
        uses: actions/checkout@v3
        with:
          submodules: recursive

      - name: 设置交叉编译器
        run: |
          sudo apt-get update
          sudo apt-get install -y gcc-aarch64-linux-gnu
          # 或者使用 Linaro 等工具链

      - name: 编译内核
        run: |
          export ARCH=arm64
          export CROSS_COMPILE=aarch64-linux-gnu-
          make oscar_user_defconfig  # 替换为你的实际 defconfig 名
          make -j$(nproc)

      - name: 打包 AnyKernel3
        run: |
          git clone https://github.com/osm0sis/AnyKernel3
          cp arch/arm64/boot/Image.gz AnyKernel3/
          # 如有 dtb 也需复制
          cd AnyKernel3
          zip -r9 ../K10x_ResukiSU_SUSFS_v2.0.0.zip ./*

      - name: 上传产物
        uses: actions/upload-artifact@v3
        with:
          name: K10x_Kernel
          path: K10x_ResukiSU_SUSFS_v2.0.0.zip# 6
