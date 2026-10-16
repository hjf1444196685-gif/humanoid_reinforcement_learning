# 下次继续工作

在有 RTX 4080 的本地 Ubuntu 电脑执行。当前 GitHub 文档不替代本地环境，先不要删除本地工作目录。

## 今天结束前

在自己启动的展示/训练终端按 Ctrl+C，等待进程结束。训练中断时不要假定最新一轮已保存，先检查检查点。无需清缓存、卸载环境或重新安装依赖。

## 明天启动

```bash
conda activate /home/zzj/TS/g1-workspace/envs/g1
cd /home/zzj/TS/g1-workspace
export PIP_CONSTRAINT="$PWD/constraints.txt"
python -m pip check
nvidia-smi
```

每次新开终端都需要激活环境。查看文本日志不要求激活；启动仿真或训练要求使用 g1 的 Python。

仅在需要安装依赖时使用以下临时源设置，不必修改永久配置：

```bash
export PIP_CONFIG_FILE=/dev/null
unset PIP_INDEX_URL PIP_EXTRA_INDEX_URL PIP_TRUSTED_HOST
```

## 重新打开静态 G1

先确认没有重复的展示进程，再在工作目录执行：

```bash
python -u view_g1_static.py
```

这只展示默认姿态，不训练、不验证动态平衡、不连接真机。图形加载可能需要等待；若卡住，收集终端日志，不要同时启动多个实例。

## 核实已有训练输出

```bash
find logs/rsl_rl/unitree_g1_29dof_velocity -type f -name 'model_*.pt' -print
tail -n 60 g1-smoke.log
```

目录不存在或没有检查点时不能声称已有可播放策略。两轮测试的策略即便保存，也不代表会走路。

## 正式训练计划（尚未确认执行）

先完成所需模型/关节检查，再由用户决定开始。关闭展示窗口后，在工作目录运行：

```bash
python -u unitree_rl_lab/scripts/rsl_rl/train.py \
  --headless \
  --task Unitree-G1-29dof-Velocity \
  --num_envs 1024 \
  --max_iterations 1000 \
  --logger tensorboard \
  > g1-train.log 2>&1
printf '训练退出码：%s\n' "$?"
```

这是新训练命令，不是从检查点恢复的命令。不要盲目重跑来恢复中断任务；先确认实际检查点和项目的 resume 参数。该规模只是第一阶段建议，尚未验证显存、速度或训练收敛。

另开终端查看：

```bash
tail -n 60 /home/zzj/TS/g1-workspace/g1-train.log
nvidia-smi
```

开始后的前 10～20 轮记录显存、采样速度和是否有错误；后续关注回合长度、速度误差和播放效果。暂不连接真机。

## 继续交流时可直接发送

> 我继续 G1 EDU 29 自由度项目。请先阅读 GitHub 仓库中的 2026-10-16 工作报告和 next-session.md。我的电脑已配置 Isaac Sim 5.1、Isaac Lab v2.3.0、PyTorch 2.7+cu128；已显示 G1 并完成 64 环境两轮 PPO 测试，正式训练未确认开始。先核实当前状态，不重复清理或重装，不连接真机。
