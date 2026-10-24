# dev-rules

AI と一緒にプロダクトを作るときの、共通のルールと手順です。
現在の版：VERSION ファイルを見よ。

## 読む順番（AI が作業するとき）
1. 各プロダクトの CLAUDE.md：作業前の確認（版の確認）と、基本の決まりの要点。版が一致すれば、ここで足りる。
2. 全作業：common-rules.md（枝の名前、別チャットの条件、止まるところ、安全）。
3. 作業に応じて：rules/ の各ファイル、dynamic-test.md、adr-template.md。どれを読むかは claude-md-block.md の「読む先の表」にある（索引は task-guide.md）。

人間が使うもの：
- prompts.md：チャットに貼る依頼文（P0〜P13）と早見表
- setup.md：新しいプロダクトの準備と、GitHub の設定チェックリスト

## どのファイルが正本か
同じ内容を複数の場所に書かないために、正本を決めています。ほかの場所には「○○を見よ」とだけ書きます。

| 内容 | 正本 |
|---|---|
| 版（VERSION の1行） | VERSION |
| 作業前の確認、読む先の表、基本の決まりの要点（CLAUDE.md に写す） | claude-md-block.md |
| 枝の名前、別チャットの条件、止まるところ、安全 | common-rules.md |
| 要件定義・設計の確定・ADR | rules/design.md |
| 実装・README | rules/impl.md |
| テスト作成・取り込み・失敗時の切り分け | rules/test.md |
| 静的レビュー | rules/review.md |
| 保守運用・新機能の追加 | rules/maintain.md |
| 文書の整理 | rules/docs-cleanup.md |
| 動的テストの手順 | dynamic-test.md |
| ADR の書き方 | adr-template.md |
| チャットに貼る依頼文（テスト作成の【前提】を含む） | prompts.md |
| 準備と設定（人間の作業） | setup.md |
| 用語と呼び方 | common-rules.md §0 |
| 作業手順の索引（決まりは書かない） | task-guide.md |

## 版の運用
- 版の正本は VERSION（1行）。ルールを直したら、VERSION を上げ、下の変更履歴に1行書く。各文書に「版：」の行は置かない（重複させると直し忘れるため）。
- 同じ変更で、claude-md-block.md と product-template の CLAUDE.md（その写し）をそろえる。片方だけ直さない。
- 各プロダクトの CLAUDE.md の「準拠する dev-rules の版」は、VERSION と同じ値にする。違うとき、AI は作業を始めず人間に知らせる（勝手に合わせない）。古い版のプロダクトは、人間が雛形の CLAUDE.md に差し替える。
- 依頼文（prompts.md）は、どのプロダクトでも「CLAUDE.md の作業前の確認に従う」の一文で始める。

## 変更履歴
- v9：#24 に対応。版の記法を「v8-test3」のような試作の形から、数字だけの「v9」に改めた（読み違いを避けるため。CLAUDE.md の命令は増やさない）。
- v8-test3（2026-10-22）：#20 に対応。静的レビュー・テスト作成・動的テストを別の新しいチャットで行うことは推奨とし、決めるのは人間にした。AI が「このチャットが作成に関わったか」を自分から確認すること（git log の確認）と、確認を取れないときに止まることをなくした（common-rules.md「別の新しいチャットで行う作業」・§1・§9、claude-md-block.md、prompts.md P0・P5・P7・P11、dynamic-test.md、setup.md）。テスト作成（P5）の「GitHub をつながない」は条件のまま。product-template の CLAUDE.md も同じ内容に差し替える。
- v8-test2（2026-10-22）：#19 に対応。書き込み権限がない環境で git bundle を出す指示が、実装のときにしか読まれなかったため、全作業で最初に読む claude-md-block.md の「基本の決まり」に要点を追記した（詳細は rules/impl.md §5 のまま）。product-template の CLAUDE.md も同じ内容に差し替える。
- v8（2026-10-21）：手順の保存の方式を決めた（#17 の論点。claude-md-block.md）。作業手順を作業ごとのファイル（rules/）に分け、作業に必要な文書だけを読む形にした（common-rules.md は核だけにする）。各プロダクトの CLAUDE.md に、作業前の確認（VERSION の1行だけを見る）・読む先の表・基本の決まりの要点を写す。版が一致すれば dev-rules の本文を毎回読まない。版の正本を VERSION に置き、各文書の「版：」の行を外す。依頼文の冒頭を「CLAUDE.md の作業前の確認に従う」に統一。P0 の参照（4.3 → 4.「未解決の問題」）、テストの枝の名前（test/《内容》）を直す。
- v7（2026-10-21）：Issue #14 #15 #16 に対応。枝の名前を「用途/内容」に変更（feature/ をやめる。common-rules §1-2）。チャットでの「確定」を有効にする（rules/design.md §3・§4、rules/design.md「設計の確定」）。進捗メモは区切りごとに push し、セッション終了前と切り替え前に必ず更新する（common-rules §1-2）。prompts・task-guide の枝の名前を新しい決まりに合わせた。#17 は未対応（保存の方式を決めてから）。
- v6（2026-10-18）：文書の整理を追加（rules/docs-cleanup.md「文書の整理」、rules/impl.md §5、依頼文 P13）。事実をどの文書に置くかの表を task-guide に置く。整理は人間の依頼でいつでも行い、AI が自発的に行うのは PR の前だけ。書き込み権限がない環境では git bundle で渡す（rules/impl.md §5、#7）。dev-rules を更新したら product-template も更新する（版の運用、#9）。
- v5（2026-10-15）：PR は原則作らない（依頼者が依頼したときだけ AI が作る）。PR を作る前に、全ドキュメントを整理する（rules/impl.md §5）。PR の状態はドキュメントに書かず、PR を作った時点で「PR #n（マージ待ち）」と書き、次の PR の整理の時点で前回分を更新する（マージ後の push はしない）。task-guide・prompts の PR 前提の記述を、依頼があったときだけに直す。
- v4（2026-10-14）：PR は作らない方針に変更（作業は枝にコミットして push。取り込みは人間が行う）。人間の指示の優先（common-rules §0-2）を追加。task-guide・prompts・setup・dynamic-test の PR 前提の記述を、枝へのコミットに置き換え。規則変更時の整合性チェックと、範囲を狭めるときの確認を追加（rules/impl.md §5）。
- v3（2026-10-14）：人間の指示を規則より優先する節（common-rules §0-2）を追加。PR を工程ごとの3本から、区切りごとの1本に変更（同時ではなく順番に AI を使う前提）。※task-guide・prompts・setup は、まだ v2 の PR 分割を前提とする記述が残っている（次版で直す）。
- v2（2026-10-14）：PR を工程ごとに 3 本（設計・実装・テスト）にまとめ、報告書は対象の PR のブランチに入れる方式に変更。README と確定項目が食い違うときは確定を正とする規則を追加。adapter の変更範囲を限定。静的レビューの観点5・6を拡張。setup に確認項目を追加。
- v1（2026-10-14）：レビューの指摘を反映。作業の流れ・止まるところ・安全の節、prompts.md、setup.md を追加。静的レビューを7観点に統合。テストの取り込み、テスト失敗の切り分け、adapter 方式を追加。
