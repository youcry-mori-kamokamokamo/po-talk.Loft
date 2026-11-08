# STATUS.md — Phase 1 Quest Browser

更新日: 2026-11-08

## 現在の状態

- ブランチ: `copilot/phase1-quest-browser`
- 作業対象: `index.html`
- フェーズ1の実装: 未着手
- `index.html`: 変更なし
- 検証: 未実施
- Quest 2実機: 未検証

## 既読の指示ファイル

- `.github/copilot-instructions.md`
- `.github/prompts/phase1-quest-browser.prompt.md`
- `CLAUDE.md`

## フェーズ1の実装項目

1. Quest Browser向けの大きな主要操作ボタン
2. 既存URL・QRと両立する短い部屋コード入口
3. 通常部屋の聞き専入口と途中参加
4. 長押しプッシュトゥトーク（既定ミュート、色と文字で状態表示）
5. 定型文と大きなリアクションボタン（自由入力は維持）

## 不変条件

- フェーズ2・フェーズ3には進まない
- `index.html`の単一ファイル構成を維持する
- Trystero、appId、データチャネルプロトコル、音声取得フローを変更しない
- `vendor/`、ライブラリバージョン、外部サーバー、録音機能は変更・追加しない
- Quest 2の2Dパネルを対象にし、hoverやWebXRに依存しない
- PC・スマートフォンの既存動作を壊さない

## 作業履歴

- 指示ファイルの存在と作業ワークツリーを確認した。
- Copilotが同じGit確認を繰り返し、STATUS.mdへ同じ内容を大量追記したため停止した。
- 元ファイルは `STATUS.md.backup-20261108` として保存した。
- 現在は復旧後の調査記録のみ。コード変更はない。

## 既存 UI（index.html で確認）

### 存在する要素
- `#join` - 通話画面のコンテナ
- `#mute` - マイクオン/オフボタン
- `#leave` - 退出ボタン
- `#reactions` - リアクションボタンのコンテナ
- `#chatText`, `#chatSend` - テキスト入力・送信ボタン
- `#raise` - 挙手ボタン
- `#speak` - 読み上げボタン

### 存在しない要素（フェーズ 1 で追加）
- `#pttBtn` - プッシュトゥトークボタン
- `#roomCode` / `#inputRoomCode` - 部屋コード入力エリア
- リスニング専用入口 UI

## フェーズ 1 の最小実装計画

### ① 部屋コード入口（既存 URL と両立）
**追加**: `<div id="roomCode" class="room-code">` + `<input id="inputRoomCode">` + `<button id="enterRoomCode">入室</button>`  
**位置**: ロビー画面の下部、既存の QR コードの下に配置  
**挙動**: 入力→「入室」で `joinRoom()` を呼び出し、空ならロビーからランダム部屋へ

### ② プッシュトゥトーク（長押し方式）
**追加**: `<button id="pttBtn" class="ptt-btn">話す</button>`  
**位置**: 通話画面の中心、`#mute` の隣に配置  
**挙動**: 
- 既定ミュート（`call.muted = true`）
- 長押し中：`call.muted = false` で音声送信、ボタン色変化（緑→赤）
- 離すと元に戻り、状態表示（「送信中」→「ミュート」）

### ③ リスニング専用入口
**実装**: 既存の `joinRoom()` に `wantMic: false` を渡すことで実現  
**UI**: 部屋コード入力エリアに「👁️ 聞き専」チェックボックスを追加  
**挙動**: チェックありで入室→`getUserMedia` を呼び出さず、音声再生のみ

### ④ リアクションボタンの追加
**現状**: `REACTIONS = ['👏', '😂', '✨', '👍', '🤔', '😮', '🎉', '🍵']` の 8 種類  
**変更なし**: 既存の `flyReaction()` / `sendReaction()` をそのまま利用

### リスクと未確認事項
- Quest Browser で長押しイベント（`pointerdown`/`pointerup`）が正しく動作するか
- 小窓モードでのプッシュトゥトーク表示領域
- iOS の自動再生制限との相性（既存の `play()` エラーハンドリングとの整合性）

## 次のステップ

1. 上記計画を確認
2. `index.html` に UI 要素を追加（部屋コードエリア、PTT ボタン）
3. PTT ボタンの長押しイベント処理を実装
4. リスニング専用入口のチェックボックスを実装
5. Quest Browser で動作確認
