# TorrentUtils = tu

所有主要功能现已能使用，欢迎试用和汇报问题，脚本参数和内部接口未来仍存在变动。

**注意**：由于 2009 年 10 月 Unicode 5.2.0 改变了部分字符的编码方式，使用 Unicode 5.2+ 与 Unicode 5.2- 编码的特殊字符如 emoji 相互不兼容。本脚本由 Python 3.8+ 继承 Unicode 12+，因此如果你使用本脚本制种并计划使用在 Unicode 5.2- 的老旧客户端如 uTorrent 2.2.1，应避免文件名中使用特殊字符。

---

## 需求

```txt
python>=3.8
tqdm (可选, 用于显示进度条)
```

直接使用发布的 exe 执行文件的话不需要以上这些。

---

## 命令行参数

```txt
$ python3 tu.py -h
usage: tu [-h] [-m {create,print,verify,modify}] [-t url [url ...]] [-c text] [-s number] [-p {0,1}]
          [--by text] [--time number] [--encoding text] [--source text] [--preset path]
          [--check-empty] [--no-progress] [--time-suffix] [-y] [--version]
          [path ...]

positional arguments:
  path                                     1 or 2 paths depending on mode

optional arguments:
  -h, --help                               show this help message and exit
  -m, --mode {create,print,verify,modify}  mode will be inferred from paths if not specified
  -t, --tracker url [url ...]              trackers can be supplied multiple times
  -c, --comment text                       your message to show in various clients
  -s, --piece-size number                  piece size in KiB (default: 4096)
  -p, --private {0,1}                      private torrent if 1 (default: 0)
  --by text                                set the creator of the torrent (default: Github)
  --time number                            set the time in second since 19700101 (default: now)
  --encoding text                          set the text encoding (default&recommended: UTF-8)
  --source text                            set the special source message (will change hash)
  --preset path                            load a preset file for metadata in creating torrent
  --check-empty                            check empty file when creating torrent
  --no-progress                            disable progress bar in creating torrent
  --time-suffix                            append current time to torrent filename
  -y, --yes                                just say yes - don't ask any question
  --version                                show program's version number and exit
```

---

## Preset 配置示例

```json
{
    "private": 0,
    "piece_size": 16384,
    "tracker_list": [
        "http://208.67.16.113:8000/annonuce",
        "udp://208.67.16.113:8000/annonuce",
        "udp://tracker.openbittorrent.com:80/announce",
        "http://t.acg.rip:6699/announce",
        "http://nyaa.tracker.wf:7777/announce",
        "https://tr.bangumi.moe:9696/announce",
        "http://tr.bangumi.moe:6969/announce",
        "udp://tr.bangumi.moe:6969/announce",
        "http://open.acgnxtracker.com/announce",
        "https://open.acgnxtracker.com/announce"
    ]
}
```

当传入的是 torrent 文件时，将使用该 torrent 作为 metadata preset。

---

## 自动模式推测和路径选取

如果没有以 -m 参数指出模式，那么将自动根据输入的路径进行推测，同时判断那个路径是用来做什么的。下表给出规则：

简写：\
路径参数：`1`/`2` = 第一个输入的路径  / 第二个输入的路径 (如果有) \
路径类型：`F`/`D`/`T` = 看起来像文件但不像种子的路径 / 看起来像目录的路径 / 看起来像种子的路径 \
操作：`L`/`R`/`W` = 由此读取文件 / 由此读取种子 / 保存种子到

| 命令行 -m 参数       | 1:F/D      | 1:T        | 1:D<br>2:F | 1:F/D<br>2:D | 1:T<br>2:F/D | 1:F/D<br>2:T | 1:T<br>2:T | 1:F<br>2:F |
| -------------------- | ---------- | ---------- | ---------- | ------------ | ------------ | ------------ | ---------- | ---------- |
| 未输入或<br>拖拽操作 | -m create  | -m print   | -m create  | -m create    | -m verify    | -m verify    | -          | -          |
| -m create            | L 1<br>W 1 | L 1<br>W 1 | L 2<br>W 1 | L 1<br>W 2   | L 2<br>W 1   | L 1<br>W 2   | R 1<br>W 2 | -          |
| -m print             | -          | R 1        | -          | -            | -            | -            | -          | -          |
| -m verify            | -          | -          | -          | -            | R 1<br>L 2   | L 1<br>R 2   | -          | -          |
| -m modify            | -          | R 1<br>W 1 | -          | -            | -            | -            | R 1<br>W 2 | -          |

---

## 感谢

<https://github.com/utdemir/bencoder>

<https://stackoverflow.com/a/31124505>
