# DVTAG

[English](README.md) | [中文](README.zh.md)

同人音声ライブラリ向けのコマンドラインタグ付けツール。フォルダ名に DLSite の作品番号（RJ/BJ/VJ）が含まれていれば、メタデータを取得してタグを書き込み、必要に応じて wav を変換します。

## できること
- `RJxxxxxx` / `BJxxxxxx` / `VJxxxxxx`（6 または 8 桁）を含むフォルダをリリースとして扱い、再帰的に探索。
- DLSite から作品名・サークル・声優・ジャンル・発売日・カバーを取得し、可能なら Chobit でタイトルと正方形サムネイルを補正。
- FLAC / M4A / MP3 にアルバム・日付・サークル・声優・ジャンル・カバー・トラック／ディスク番号を付与。
- よくあるファイル名パターンに基づいてトラック名の番号プレフィックスを削除しようとします。
- 任意で `.wav` を `.flac` または `.mp3`（320k）へ ffmpeg で変換し、成功後に元の wav を削除。

## 必要環境
- Python 3.9+
- ffmpeg（変換フラグ使用時のみ）
- dlsite.com と chobit.cc へのネットワークアクセス

## インストール

```bash
pip install dvtag            # または pipx install dvtag
pip install --upgrade dvtag  # アップグレード（pipx upgrade dvtag）
```

## クイックスタート
1) 各リリースのフォルダ名に作品番号を含めてください（例: `RJ123456`, `rj123456 bonus`, `xxx_RJ01123456`）。
2) 次を実行します:

```bash
dvtag /path/to/your/library        # タグ付けのみ
dvtag -w2f /path/to/your/library   # タグ付け + wav→flac 変換
dvtag -w2m /path/to/your/library   # タグ付け + wav→mp3 変換（320k）
```

## CLI リファレンス

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

## 動作と現在の癖
- **ディスカバリ**: 指定ディレクトリを走査し、作品番号を含むフォルダが見つかったらその直下だけをタグ付けし、さらに深くは潜りません。
- **メタデータ**: DLSite から基本情報を取得し、Chobit で短いタイトルと正方形サムネイルを試みます。
- **カバー選択**: 現状は Chobit の正方形サムネイルを優先しますが、DLSite の標準的な横長カバーの方が見栄えすることもあります。ユーザーが切り替えられる設定が必要です。
- **ジャンル順序**: DLSite の順序を保持します。同じ集合でも並びが違うと再書き込みが起こる場合があり、より賢い比較が必要です。
- **トラック名**: よくある番号付きファイル名を見つけたときだけ番号を除去し、合わない場合はファイル名の stem をそのまま使います。
- **ログ**: 現在は最低限のコンソールログのみ。詳細化・構造化は課題です。
- **冪等性**: タグが変わるときだけ書き込みますが、上記の課題が残ると無意味な書き込みが発生することがあります。

## 既知の課題とアイデア
- **キャッシュなし**: 大きなライブラリを繰り返し処理すると毎回 DLSite/Chobit にアクセスし、時間と負荷が増えます。期限付きのローカルキャッシュを追加したい。
- **ジャンル比較**: 並びの違いだけで再書き込みしないよう、集合比較や順序保持を工夫したい。
- **ログと可観測性**: 構造化ログ、進捗表示、より明確なエラーメッセージ。

## ライセンス
[MIT](LICENSE)
