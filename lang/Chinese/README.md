<p align="center">
  <a href="https://github.com/HatcherMine/Raptoreum-Miner">English</a> |
  <a href="https://github.com/HatcherMine/Raptoreum-Miner/edit/main/lang/Russian">Pусский</a> |
  <span>中文</span> |
</p>

下载
------------
可以在找到下载 [此处](https://github.com/HatcherMine/Raptoreum-Miner/releases)。

要求
------------

1. 至少支持 SSE2 的 x86-64 架构 CPU。这包括 Intel Core2 和更新版本以及 AMD 等效产品。针对具有 AES、AVX、AVX2、SHA、AVX512 和 VAES 的 CPU 的某些算法提供了进一步的优化。

目前尚不支持 ARM 和 Aarch64 CPU。

2. 64 位 Linux 或 Windows 操作系统。众所周知，基于 Ubuntu 和 Fedora 的发行版（包括 Mint 和 Centos）可以工作并且在其存储库中具有所有依赖项。其他人可能工作，但可能需要更多的努力。由于缺少功能，旧版本（例如 Centos 6）无法运行。 mingw-w64 和 msys 或预构建的二进制文件支持 64 位 Windows 操作系统。

不支持 MacOS、OSx 和 Android。

3. Stratum池支持stratum+tcp://或stratum+ssl://协议或使用http://或https://的RPC getwork。 GBT 是 YMMV。

支持的算法
--------------------


                          gr 幽灵骑士 (RTM)
                           

快速设置
-----------

      要添加或使用矿工的选项，请使用包含的 config.json 文件。
      所有选项都应以 JSON 格式显示，例如：
      "long-flag-name": "Some_value"

      一些例子：
      “全调”：真的
      “调整配置”：“some_filename”
      "url": "stratum+tcp://YOUR_POOL_ADDRESS:PORT"
      “用户”：“你的钱包”

有关完整的矿工选项列表和其他提示，请阅读 readme.txt 文件。

调音
------
调整随着矿工的启动自动开始。如果先前的调整文件`tune_config` 存在（或使用`--tune-config=FILE` 标志），则使用它。这种行为可以被 `--no-tune` 或 `--force-tune` 覆盖。在非 AVX2 CPU 上，默认调整过程大约需要 69 分钟才能完成。在 AVX2 CPU 上，默认调整过程大约需要 155 分钟才能完成。

关于调优作用的小说明。传统的散列方法是，获取一些输入，对其进行散列以生成输出散列。这就是所谓的正常散列（又名 1way），因为我们一次进行 1 个散列。我们可以做的是同时散列 2 或 4 个散列！由于每个块等中使用的变体不同，内存需求会发生变化，我们希望在缓存中尽可能多地使用它，因为 RAM 很慢！因此，如果我们想解决它，1way 需要将 128KiB、256KiB、256KiB、512KiB、1MiB 或 2MiB 存储在某处。 2way 将需要该数量的 2x 和 4way 4x。在某些情况下，例如在 256KiB 变体中，这会将要求增加到 1MiB 或 512KiB 变体的 2MiB。在大多数情况下，此数量的数据可以放入缓存中，从而带来额外的性能。放太多会减少它想象 8MiB 对于 4way Fast（2MiB 变体）（在某些情况下，如果您的 CPU 缺少缓存，它仍然是最快的，例如 i3 或某些移动 CPU）。好的，所以有 6 种变体，可以进行 20 次可能的“旋转”。我们检查所有这 20 次旋转，看看我们对每个旋转使用 1way、2way 或 4way 是否会带来改进。我们无法单独检查它们何时使用所有散列进行散列，它可能不再准确，因此我们对 AVX 每次旋转有 8 个场景（只有 1way 或 2way、2^3、2 种解决 3 个变体的方法）和AVX2 为 27（1 路、2 路、4 路、3^3、3 种变体的 3 种解决方法）-> 这是 --tune-full 可以检查所有内容。 --tune-simple 仅检查 Turtle 和 Turtlelite 变体上的 4way，默认情况下还会检查 Dark 和 Darklite 的，因为它们最有可能从我们所有的测试中使用或使 CPU 受益。这样我们就可以使用最多的缓存并尽可能高效地使用它。您可能会问的问题是，如何做 2way 比只做 2 次 1way 快？那是因为我们可以使用更多的并行化和其他技巧来通知 CPU 即将发生的事情，以便它可以更快地准备数据。
