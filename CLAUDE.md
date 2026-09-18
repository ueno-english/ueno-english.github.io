# ueno-english.github.io

中学3年生（New Treasure）向けの英語復習サイト。生徒がスマホで文法解説・本文和訳・単語テスト・
実力テスト/定期テスト対策を復習できる。全ページ単体完結の静的HTMLで、GitHub Pages で公開している。

## 公開方法（deploy）

- リポジトリ = GitHub Pages のユーザーサイト: `github.com/ueno-english/ueno-english.github.io` → https://ueno-english.github.io
- **公開手順は `git add` → `git commit` → `git push origin main` だけ。ビルド不要。** push の1〜2分後に反映。
- ブランチは `main` のみ。サイトはリポジトリ直下（`/`）をそのまま配信。
- 作業開始時に `git status` を確認する。ローカルに push し忘れたコミットが残っていることがある。

## ファイル構成

外部CSS/JSファイルは無し。`<style>` と `<script>` は各HTMLに直書き。

| ファイル | 種類 | 内容 |
|---|---|---|
| `index.html` | ハブ（目次） | 全ページへのカード一覧 ＋ 文法検索バー |
| `lessonXX-Y.html` | 文法解説 | Key Point 解説 → 練習問題の答え → 確認クイズ |
| `lessonXX-Y-honbun.html` | 本文 | Scene 会話の和訳 ＋ 表現メモ ＋ 語句まとめ ＋ 英問英答 |
| `lessonXX-Y-2.html` | 補足プリント | 特定文法の追加練習（例: `lesson11-3-2.html` = so〜that 言い換え） |
| `lessonXX-vocabN.html` | 単語テスト | 30語 4択クイズ |
| `honbun-cloze.html` | 本文穴あき | Lesson 11-1〜12-3 の本文クローズ（タップで答え表示） |
| `teiki-test.html` | 定期テスト対策 | 並び替え・空欄補充・全文記述 |
| `jitsuryoku-N.html` | 実力テスト対策 | 大問1〜5（ベネッセ学力推移調査 中3・9月 対応） |

現在の範囲: Lesson 11（不定詞の発展）、Lesson 12（間接疑問・付加疑問・仮定法過去）。今後レッスンを追加していく。

## デザイン規約（全ページ共通）

- 配色は `:root` の CSS変数。**変数名 `--blue` は実際にはピンク/マゼンタ `#b5006e`**（サイトのテーマカラー）。
  ほかに `--red #c0392b` / `--green #1a7a1a` / `--gold #d4700a` / `--bg #fff0f6` / `--border #f5c6df`。
- フォント: `"Hiragino Sans", "Meiryo", sans-serif`、body は `font-size:15px; line-height:1.7`。英文表示は `Georgia, serif`。
- スマホ前提のレイアウト。`main { max-width: 720px; margin: 0 auto; }`。
- ヘッダーはピンクのグラデーション ＋ 絵文字付き `<h1>`。
- 文法ページ ⇔ 本文ページは相互に `.back-btn` でリンクする（例: 本文ページに「← 文法解説ページへ」）。
- `<head>` に共通のセキュリティ meta 4種 ＋ CSP。**CSP は `gc.zgo.at`（GoatCounter アクセス解析）を許可済み。消さない。**
- `</body>` 直前に GoatCounter スニペット（27ファイル全部に入っている）:
  ```html
  <script data-goatcounter="https://ueno-english.goatcounter.com/count" async src="//gc.zgo.at/count.js"></script>
  ```

## ページ内の作り

- **共通テンプレートは無い。** 各ページが独自の `<script>` を持ち、クイズ関数の名前もページごとにバラバラ
  （`startQuiz` は共通だが `showQ`/`showQuestion`、`nextQ`/`nextQuestion`、`answer`/`selectAnswer` など揺れている）。
- → **新規ページは同じ種類の既存ページを丸ごとコピーして作る。** JS も一緒にコピーし、1ページ内で関数名を混在させない。
- 文法解説ページ: `<div class="section" id="kpN">`（Key Point）→ `id="ex"`（練習問題の答え、`toggle()` で開閉）
  → `<div class="quiz-section" id="quiz">`（確認クイズ）。
- 本文ページ: `.scene-desc`（場面説明）→ `.speech-block` を話者ごとに並べる
  （`.speech-header` に `.speaker-badge` ＋ `.line-num`、`.speech-body` に `.eng` / `.ja` / `.point`）
  → `.summary-box`（語句まとめ）→ `.qanda-section`（英問英答、ボタンは `onclick="toggleQA(this)"`）。
- 本文の重要表現は `.hl-blue` `.hl-red` `.hl-green` `.hl-gold` `.hl-purple` でハイライト。

## 新しいページを追加するときのチェックリスト

1. 同じ種類の既存ページをコピーして中身を差し替える。
2. `<title>` を更新（形式: `Lesson XX-Y 解説｜上野作 英語復習活用ホームページ`）。
3. 文法 ⇔ 本文ページなら `.back-btn` の `href` を対応先に直す。
4. GoatCounter スニペットと CSP meta がそのまま残っているか確認。
5. **`index.html` にカードを追加する（忘れると目次から辿れない孤立ページになる）:**
   - 該当 `.unit` の `.lesson-grid` 内に `<a class="lesson-card" href="..." data-keywords="...">` を追加。
   - `data-keywords` に検索でヒットさせたい語（文法用語・英単語）を入れる。`index.html` の `filterCards()` が
     この属性 ＋ 表示テキストで絞り込む。
   - 新着は `<span class="badge-new">NEW</span>`、未完成は `class="lesson-card coming-soon"`。
6. 動作確認: ブラウザで開き、クイズ・答え表示ボタン・検索が実際に動くか見る。
7. `git add -A && git commit -m "..." && git push origin main`。

## 過去に繰り返したミス（要注意）

- **本文ページで `toggleQA()` 関数の入れ忘れ。** 英問英答のボタンは `onclick="toggleQA(this)"` で、`</body>` 前に
  関数定義が要る（過去に lesson11-2, 11-3 で欠落 → 修正済み）。コピー元にこの関数があるか必ず確認。
- **話者名の取り違え。** Lesson 11-3 本文の店員は **"Clerk"**（過去に "Clark" と誤記 → 修正済み）。
  ただし **CSSクラス名は今も `.speaker-badge clark`** のまま。表示テキストは "Clerk"、クラス名は `clark`。混同しない。
  登場人物: Yumi / Kevin / Mrs. Baker / Carlos / Clerk。
- **セリフの行の欠落**（例: 11-3 の "Can I try it on?" が抜けていた）。本文を差し替えるときは教科書と1行ずつ突き合わせる。
- **本文と `honbun-cloze.html` の不一致。** 本文ページを直したら `honbun-cloze.html` の該当箇所も直す。
- 細かい前置詞・冠詞の脱落（例: "listening to music" の "to"）。英文はコピペ後に音読チェックする。
