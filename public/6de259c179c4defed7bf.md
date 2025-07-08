---
title: bashでファイル名の探索とその備忘録
tags:
  - Bash
private: false
updated_at: '2025-07-08T21:08:27+09:00'
id: 6de259c179c4defed7bf
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要

Bashで現在のディレクトリから上の階層へ向かってファイル名での探索を行いたい。
今回は、とある目的からpackage.jsonを探索することにしています。
普段bashで開発することが少ないため、補足にbashではまったことをメモしています。

# 環境
- bash

# 探索方法

探索のイメージは、こんな感じで上位の階層に向かって、ファイルが見つかるまで`ls`を繰り返して探索します。

```bash
$ ls ./package.json
$ ls ./../package.json
$ ls ./../../package.json
$ ls ./../../../package.json
```

# 完成したもの

```bash
#!/bin/bash

# カレントディレクトリから上の階層へ向かってファイルの探索を行う
# 現在居るディレクトリからのファイルのPathを${findFile}にセットする
#
# Examples:
#   fileSerachClimb 'package.json'
#   echo ${findFile}
#   unset findFile
# Args:
#   $1 - 検索するファイル名
# Return:
#   0 - Error 
function fileSerachClimb(){
    # 探索するファイル名
    local fileName=$1
    # 現在のPathを保存
    local cd=`pwd`
    # 上の階層への探索の上限を取得
    local repeatCount=$((`echo -n ${cd} | sed -e 's@[^/]@@g' | wc -c`-1))

    # ループの回数
    local count=0
    # ループした回数分`../`を結合
    local climbStr=''

    while [[ ${repeatCount} -ge ${count} ]]; do
        # lsで対象のファイルが存在するかチェック
        if [[ ${count} -eq 0 ]]; then
            ls ./${fileName} >& /dev/null
        else
            climbStr="../"${climbStr}
            ls ${climbStr}${fileName} >& /dev/null
        fi

        # lsが成功したらfindFileにPathを格納する
        if [ $? = 0 ]; then
            findFile="${climbStr}${fileName}"
            break
        fi
        count=$((${count}+1))
    done
    return 0
}
```

# 補足

- bashの関数は`return`で文字列を返せない
  - globalな変数(`fildFile`)にセットすることで関数の外側でも参照できる
  - 探索失敗したときは、0を返すようにする
- 関数内の`local`は、関数内のみで使える
  - localで定義しないとglobal変数にセットされてしまうので注意
  - global変数に定義して使い終わったものは、globalを汚染しない様に`unset`してしまうのがオススメ
- 基本的に`ls`はエラーになるので、`>& /dev/null`はエラー内容を出力しないようにしている

# おわり

以上です。もっといい方法があれば教えていただければ嬉しいです。




