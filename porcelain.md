## 歴史の書き換え

### 直近のコミットを書き換え
```bash
git commit --amend
```

### さらに歴史をさかのぼったコミットを変更
```bash
git rebase -i HEAD~3
```

### 全コミットからのファイルの削除
1. 誤って秘匿情報をpushしてしまった場合
1. 巨大なバイナリファイルをあげてしまった場合
```bash
# 'passwords.txt'を削除する（各チェックアウトに対してコマンドを実行し、結果を再コミットする）
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD

# 全てのブランチ（--all）に実行（ツリーをチェックアウトしないでindexを書き換えるので高速）※1
git filter-branch -f --index-filter "git rm -rf --cached --ignore-unmatch .env" --prune-empty -- --all

# リポジトリを最適化(不要なオブジェクトを削除)
git gc --aggressive --prune=now

# コミットのハッシュ値が変わるので強制プッシュする
git push -f origin main
```
※1 フォルダ名を指定する場合は'foo/'
[ドキュメント：git-filter-branch](https://git-scm.com/docs/git-filter-branch)

### メールアドレスの一括変更
```bash
git filter-branch --commit-filter '
        if [ "$GIT_AUTHOR_EMAIL" = "schacon@localhost" ];
        then
                GIT_AUTHOR_NAME="Scott Chacon";
                GIT_AUTHOR_EMAIL="schacon@example.com";
                git commit-tree "$@";
        else
                git commit-tree "$@";
        fi' HEAD
```
