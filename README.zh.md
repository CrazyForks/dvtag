# DVTAG

[English](README.md) | [日本語](README.ja.md)

面向同人音声库的命令行标签工具。只要文件夹名里包含 DLSite 作品号（RJ/BJ/VJ），就会抓取元数据写入标签，并可选择性地转换 wav。

## 能做什么
- 递归查找名称包含 `RJxxxxxx` / `BJxxxxxx` / `VJxxxxxx`（6 或 8 位）的目录，并把它们当作一部作品。
- 从 DLSite 获取作品名、社团、声优、类型、发售日、封面，并在可能时用 Chobit 补充更短的标题和方形缩略图。
- 给 FLAC / M4A / MP3 写入专辑、日期、社团、声优、类型、封面、曲序/碟号。
- 尝试根据常见文件名模式去掉轨道名里的编号前缀。
- 可选：用 ffmpeg 把 `.wav` 转成 `.flac` 或 `.mp3`（320k），成功后删除源 wav。

## 环境要求
- Python 3.9+
- ffmpeg（仅在使用转换选项时需要）
- 可访问 dlsite.com 和 chobit.cc 的网络

## 安装

```bash
pip install dvtag            # 或 pipx install dvtag
pip install --upgrade dvtag  # 升级（pipx upgrade dvtag）
```

## 快速开始
1) 每个作品的文件夹名里放入作品号（如 `RJ123456`, `rj123456 bonus`, `xxx_RJ01123456`）。
2) 运行：

```bash
dvtag /path/to/your/library        # 只打标签
dvtag -w2f /path/to/your/library   # 标签 + wav→flac
dvtag -w2m /path/to/your/library   # 标签 + wav→mp3（320k）
```

## CLI 参考

```
usage: dvtag [-h] [-v] [-w2f] [-w2m] dirpath

Doujin Voice Tagging Tool (tagging in place)

positional arguments:
  dirpath        a required directory path

options:
  -h, --help     show this help message and exit
  -v, --version  show program's version number and exit
  -w2f           transcode all wav files to flac [LOSELESS]
  -w2m           transcode all wav files to mp3
```

## 工作方式与当前缺点
- **发现规则**：遍历指定目录，一旦遇到包含作品号的目录，就在该层进行标签处理，不再向下递归。
- **元数据**：从 DLSite 拉取基础信息；再调用 Chobit 以获取更短的标题和方形缩略图（如果有）。
- **封面选择**：目前优先使用 Chobit 的方形缩略图，但不少作品用 DLSite 的默认横版封面更好看。需要提供让用户切换的选项。
- **类型顺序**：保持 DLSite 给出的顺序。同一集合但顺序不同可能仍会触发重写，需要更聪明的比较方式。
- **轨道名**：匹配常见编号模式时去掉编号，否则直接用文件名的 stem。
- **日志**：只有最基本的控制台日志；需要改进层级、结构化和错误提示。
- **幂等性**：只有检测到标签变化才写入，但上述缺口仍可能导致无意义的重写。

## 已知缺陷与规划
- **缓存缺失**：重复处理大库会每次都请求 DLSite/Chobit，耗时且增加负载。计划加入带有效期的本地缓存。
- **类型比较**：把类型列表视作集合（或提供配置）以避免仅顺序不同就重写，同时保持原始顺序。
- **日志与可观测性**：结构化日志、进度提示、更清晰的错误处理。

## 许可证
[MIT](LICENSE)
