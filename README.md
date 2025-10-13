# config_task

- [Task](https://taskfile.dev/)
- 2025-09-16 v3.45.3 のリリースで `$XDG_CONFIG_HOME/task/taskrc.yml` が使用可能になったのでテスト

# usage

```bash
cd ~/.config  # $XDG_CONFIG_HOME or $HOME/.config
git clone https://github.com/officel/config_task.git ./task

# or I do it this way
cd ~/repos/github.com/officel/
git clone https://github.com/officel/config_task.git
ln -s ~/repos/github.com/officel/config_task/ ~/.config/task
```

using global tasks, copy them (of course, it's up to you).

```bash
cd
ln -s ~/.config/task/Taskfile.yml

alias t='task'
t
```

# note

- [go-task/task: A task runner / simpler Make alternative written in Go](https://github.com/go-task/task)
- [モダンなタスクランナーを求めて task (taskfile.dev) を使うまでの軌跡](https://zenn.dev/raki/articles/2024-05-30_task_runner)
- [Taskfile.yml のスタイルガイドに沿ったテンプレート](https://zenn.dev/raki/articles/2024-11-19_taskfile_style_guide)

# related my projects

- [officel/config_aqua: .config/aqua](https://github.com/officel/config_aqua)
- [officel/config_bash: .config/bash](https://github.com/officel/config_bash)
- [officel/config_git: .config/git](https://github.com/officel/config_git)
- [officel/config_task: .config/task](https://github.com/officel/config_task)
