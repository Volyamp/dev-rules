# 手順：プロダクトの CLAUDE.md を dev-rules の版に合わせる（v13）

対象：人間が「更新せよ」と指示したとき。作業前の確認で版が違うと分かっても、AI は自分でこの手順を始めない。違うことを人間に報告して止まる。

正本は dev-rules の claude-md-block.md。プロダクトの CLAUDE.md の「作業前の確認」「読む先の表」「基本の決まり（要点）」は、正本から生成する。手で書き写さない。製品固有の節（「このプロダクトだけの約束」以降）は触らない。

## 手順

1. 目標の版を確定する。dev-rules の main の VERSION を読む（`cat VERSION`。ローカルになければ `curl -sS https://raw.githubusercontent.com/Volyamp/dev-rules/main/VERSION`）。出た文字列（例：v13）を報告に書く。人間が版と枝を指定したときは、その指定を目標の版とし、報告に指定の内容を書く（main の版と違うことも併せて書く）。

2. 正本を取る。目標の版と同じ VERSION を持つ commit の claude-md-block.md を使う（通常は main）。人間が枝を指定したときは、その枝の claude-md-block.md を使い、報告に枝の名前を書く。
   ```
   git -C <dev-rules> show <commit>:claude-md-block.md > /tmp/cmb.md
   ```
   （/tmp は作業用の一時ファイルのため、ここでだけ使う。）

3. 正本の節を抜き出し、見出しの階層を「##」にそろえる。
   ```
   sed -n '/^### 作業前の確認/,$p' /tmp/cmb.md | sed 's/^### /## /' > /tmp/block.md
   ```

4. 製品の CLAUDE.md を組み立てる。先頭（「# 開発ルール（このプロダクト用）」、空行、「準拠する dev-rules の版：<手順1の版>」、空行）、/tmp/block.md、空行、そして既存の「## このプロダクトだけの約束」以降をそのまま続ける。
   ```
   { printf '# 開発ルール（このプロダクト用）\n\n準拠する dev-rules の版：%s\n\n' "<版>"; cat /tmp/block.md; echo; sed -n '/^## このプロダクトだけの約束/,$p' CLAUDE.md; } > /tmp/claude-new.md && mv /tmp/claude-new.md CLAUDE.md
   ```

5. 確認する。
   - 正本の節と CLAUDE.md の該当部分が一致すること（空差分。空行の違いは無視する）。
     ```
     awk '/^## 作業前の確認/{p=1} /^## このプロダクトだけの約束/{p=0} p' CLAUDE.md > /tmp/in-product.md
     diff -B /tmp/in-product.md /tmp/block.md
     ```
     差分が出たら、手順3・4からやり直す。手で直さない。
   - 版の表記が手順1の版と一致すること（`grep -n "準拠する dev-rules の版" CLAUDE.md`）。
   - 製品固有の節が変わっていないこと（`git diff` で「このプロダクトだけの約束」以降に差分がないこと）。

6. 作業ブランチに commit して push する。枝は「docs/《内容》」とし、main には直接書かない（人間の指示による例外がある場合を除く）。push 後は claude-md-block.md の「push した後は」の三手順を行い、フックが exit 0 で通ることを確かめる。

7. 報告する。次の四つを書く。
   - 目標の版（手順1の文字列）と、正本の出どころ（commit と枝の名前）。
   - 作業ブランチの名前と、確認の結果（手順5の差分が空かどうか、フックの結果）。
   - main の版（`git show origin/main:CLAUDE.md` の版の行）。main が目標の版と違うときは、その差を書き、「main への取り込みは人間が行う」と書く。
   - 「完了」と書いてよいのは、手順5の差分が空で、手順6が通ったときだけ。
