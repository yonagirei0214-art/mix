# UTA MIX CONSOLE

歌ってみた録音をブラウザだけでミックスするツール（GitHub Pages配信・サーバー不要）。

## できること（MVP）
- **EQ**: 3バンド（Low 200Hz / Mid 1kHz / High 4kHz、各±12dB）
- **リバーブ**: Tone.jsのアルゴリズムリバーブ（外部IRファイル不要、Decay時間可変）
- **オクターブ下**: フォルマント保持なしの単純ピッチシフト（Tone.PitchShift, -12st）
- **ハモリ**: 固定インターバル（短3度/長3度/完全5度上、完全4度下）でのピッチシフト
- 全処理は `OfflineAudioContext` 上で実行 → 完了後にWAVでダウンロード

## 使い方
1. `index.html` を開く（GitHub Pagesにデプロイ、またはローカルで直接開いてもOK）
2. 音声ファイルをドロップ
3. 各チャンネルのフェーダーを調整
4. 「MIX DOWN」を押すとレンダリング開始
5. 完了したらプレビュー再生 → WAVダウンロード

## GitHub Pagesへのデプロイ
```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin <あなたのリポジトリURL>
git push -u origin main
```
リポジトリの Settings → Pages → Source を `main` ブランチ / ルートに設定するだけで公開されます。

## 既知の制限（次フェーズの課題）
これらは会話で洗い出した既知の課題で、現バージョンでは未対応です。

- **ピッチ補正（オートチューン的な機能）は未実装**。ピッチ検出→スケール量子化→再合成が必要で、実装難度が別格に高いため別フェーズで対応予定
- **オクターブ下・ハモリはフォルマント保持なし**。声質が変わる（こもる／別人っぽくなる）ことがある。フォルマント保持には位相ボコーダーやPSOLA相当の実装が必要
- **ハモリは固定インターバル**。曲のキー/コード変化に追従しないため、曲によっては不協和音になる箇所がある
- **スマホブラウザでの処理負荷**は曲の長さ・有効エフェクト数に応じて重くなる。3〜5分の曲 + 全エフェクトONだと数十秒〜1分程度かかる想定
- 対応フォーマットはブラウザの `decodeAudioData` に依存（wav/mp3/m4a等は概ね可、一部コーデックは非対応の場合あり）

## 技術構成
- Web Audio API / [Tone.js](https://tonejs.github.io/) (`Tone.Offline` でのオフラインレンダリング)
- WAVエンコードは外部ライブラリなしの自前実装（`js/wav-encoder.js`）
- CDN依存: Tone.js（`cdnjs.cloudflare.com`）以外、ビルド不要・npm不要
