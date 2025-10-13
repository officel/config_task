# taskfile note

## 追加のルール

- 以下、タスクファイル名は `Taskfile.yml` と `Taskfile.dist.yml` として表記し、先頭の大文字小文字、拡張子の `a` の有無は問わない（好きに読み替える）ものとする
- 以下、チームは、組織・グループ・プロジェクト・リポジトリなどと同義として扱う。個人であっても何かしらの集まりであっても再利用可能な汎用性を持たせている

### チームで共通のタスクは `Taskfile.dist.yml` にする

- チームで共通のファイルであることを表現できる（dist）
- 優先順位の低い自動読み込みのファイル名にすることで、すべてのユーザが（とくになんの考慮もなく）使用できる
- 自前のタスクを用意していない人もファイルの指定なしで読み込まれる
- 自前のタスクを用意している人も自分でインクルードできる
- 個人でソロ利用の場合もリポジトリ固有のタスクを切り出しておくとよい

### 個人用のタスクを追加するには `Taskfile.yml` を使用する

- チームで使用する場合は `.gitignore` に `Taskfile.yml` を追加しておく（このファイルを各自用として使っていいことを示唆できる）
- `Taskfile.yml` を管理したい場合は、別のリポジトリに実ファイルを配置し、プロジェクトにはシンボリックリンクをはることで対応できる

```bash
examples/001 $ tree
.
|-- my-repo
|   `-- Taskfile.yml
`-- team-repo
    |-- Taskfile.dist.yml
    `-- Taskfile.yml -> ../my-repo/Taskfile.yml
```

蛇足だが好きに管理できることも示しておく

```bash
team-repo $ ln -s ../my-repo/Taskfile_team-repo.yml Taskfile.yml
examples/002 $ tree
.
|-- my-repo
|   `-- Taskfile_team-repo.yml
`-- team-repo
    |-- Taskfile.dist.yml
    `-- Taskfile.yml -> ../my-repo/Taskfile_team-repo.yml
```

### （廃案）チームで共通のタスクファイルには `default` タスクを書かない

- `default` とするタスクは個人の裁量で決定できるべき
- と思っていたのでタスク名が重複しているとエラーになって困っていたが
- `Taskfile.yml` で `Taskfile.dist.yml` を `include` して `default` タスクを書けば、各自で置き換えが可能
- 次の要領で、各自の `default` とチームの `dist:default` を分けることができる
- （`flatten: true` にするとタスク名重複エラーになるので注意する）
- というわけでチーム共通のタスクファイルにも `default` を書いて大丈夫

```yaml:Taskfile.yml
includes:
  dist:
    taskfile: ./Taskfile.dist.yml
    # optional: true
    # internal: false
    # flatten: false
tasks:
  default:
    cmds:
      # 自前のデフォルトタスクを書いてもいいし次のようにチームのデフォルトタスクを呼んでもいい
      - task dist:default
    silent: true
```

### チームのタスクではできるだけ1つのタスクで1つのことをする

- もちろんまとめて実行するタスクを用意してもよい
- 各自のタスクファイルで必要なことだけできるように組み合わせを選べるようにする
- 選ばなくても問題がないように作れるならそれでよし
- 外部スクリプト化して組み合わせを選択できないような仕組みはよくないと思う

### 変数名は大文字、複数語の場合はアンダーバーを使用する（スクリーミングスネークケース）

- タスク名は `kebab-case` を使用することになっている（小文字＋ハイフン）
- 変数名は `UPPERCASE LETTERS` を使用することになっている（大文字のみ）
- とくに指定はないので、複数単語の変数名は `SCREAMING_SNAKE_CASE` を使用することを明示する（勝手）
- ちなみに `special variables` と `environment variables` もスクリーニングスネークケースなので、暗黙的に指示されているともいえる
- 命名の競合と混乱を避けるために、`MY_` プレフィックス等で工夫するとよい

### 複数のタスクファイルを管理する場合は、`Taskfile.XXXX.yml` とし、インクルードする際に `XXXX` を使用する

- とくに指定はないので、`dist` と並べて違和感がなく、管理しやすいように命名する
- `include` のキーと同じ命名にしておけば混乱しない

```yaml:Taskfile.yml
includes:
  dist:
    taskfile: ./Taskfile.dist.yml
```

- たとえば個人のプロジェクトで単一のリポジトリにいろいろ突っ込んでいる場合（俺だ）に次のように整理するとわかりやすい

```yaml:Taskfile.dist.yml
includes:
  frontend:
    taskfile: ./Taskfile.frontend.yml
  backend:
    taskfile: ./Taskfile.backend.yml
  infra:
    taskfile: ./Taskfile.infra.yml
```

```bash
examples/003 $ tree
.
|-- my-project-repo
|   |-- Taskfile.backend.yml
|   |-- Taskfile.dist.yml
|   |-- Taskfile.front.yml
|   `-- Taskfile.infra.yml
`-- my-repo
    `-- Taskfile.my-project.yml
```

### 共通のタスクファイルはわかりやすさを優先して記述し、個人用のタスクファイルで省略するとよい

- チームで共通のタスクファイルは省略をせずに明示的にする
- 個人用のタスクファイルはわかれば好きにするとよい

```bash
my-project-repo $ t
task: Available tasks for this project:
* b:a:                   Clean,Build,Deploy backend
* backend:build:         Build       (aliases: backend:b)
* backend:clean:         Clean       (aliases: backend:c)
* backend:deploy:        Deploy      (aliases: backend:d)
* f:a:                   Clean,Build,Deploy frontend
* frontend:build:        Build       (aliases: frontend:b)
* frontend:clean:        Clean       (aliases: frontend:c)
* frontend:deploy:       Deploy      (aliases: frontend:d)
* i:a:                   Clean,Build,Deploy infra
* infra:build:           Build       (aliases: infra:b)
* infra:clean:           Clean       (aliases: infra:c)
* infra:deploy:          Deploy      (aliases: infra:d)
```
