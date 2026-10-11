# OSTEP Ch.4 Homework Answers

## Q1
- Prediction / 预测:
Total Time: 10 ticks，CPU Busy: 10，CPU Utilization: 100%
- Reasoning / 理由:
PID0与PID1均为纯CPU密集进程 `-l 5:100,5:100`，没有I/O事件。默认调度策略`SWITCH_ON_IO`，仅发生I/O才触发进程切换，无I/O时进程运行至结束才让出CPU。PID0连续执行5 tick完成，随后PID1连续执行5 tick。全程CPU始终被占用，不存在CPU空闲时刻。
Verified result / 验证结果:
Stats: Total Time 10
Stats: CPU Busy 10 (100.00%)
Stats: IO Busy  0 (0.00%)

- Analysis / 分析:
## Q2
- Prediction / 预测:
Total Time: 11 ticks，CPU Busy: 6，CPU Utilization: 54.55%
- Reasoning / 理由:
进程配置 `-l 4:100,1:0`。PID0是4条CPU指令，优先运行，连续4 tick执行完毕。PID1执行一次完整I/O：`RUN:io(1 tick)` + BLOCKED(5 tick) + `RUN:io_done(1 tick)`，I/O合计7 tick。PID0结束后才启动PID1；PID1阻塞期间没有就绪进程，CPU空闲。总时间 = 4 + 7 =11；CPU忙碌tick = 4(PID0)+2(I/O相关CPU操作)=6。
Verified result / 验证结果:
Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
- Analysis / 分析:
## Q3
- Prediction / 预测:
Total Time: 7 ticks，CPU Busy: 6，CPU Utilization: 85.71%
- Reasoning / 理由:
进程配置 `-l 1:0,4:100`，默认`SWITCH_ON_IO`。PID0 t1执行`RUN:io`发起I/O，触发进程切换，PID0进入5 tick的BLOCKED状态。在I/O阻塞的前4个tick，CPU调度运行PID1，PID1执行完毕。t6 PID0依旧BLOCKED，CPU空闲；t7执行`RUN:io_done`完成PID0。I/O等待时间与PID1计算时间重叠，总时间缩短为7 tick。CPU忙碌tick=2(PID0 I/O) +4(PID1)=6。
Verified result / 验证结果:
Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%)
- Analysis / 分析:
## Q4
- Prediction / 预测:
Total Time: 11 ticks，CPU Busy: 6，CPU Utilization: 54.55%
- Reasoning / 理由:
配置 `-l 1:0,4:100 -S SWITCH_ON_END`。`SWITCH_ON_END`策略：只有进程完全结束才切换CPU。PID0发起I/O进入BLOCKED，不会发生进程切换，CPU空闲等待PID0整个I/O流程全部结束。PID0完成后才调度PID1运行4个CPU tick。总时间=7(I/O完整周期)+4(PID1)=11，CPU Busy依旧为6。
Verified result / 验证结果:
Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
- Analysis / 分析:
## Q5
- Prediction / 预测:
Total Time: 7 ticks，CPU Busy: 6，CPU Utilization: 85.71%
- Reasoning / 理由:
配置 `-l 1:0,4:100 -S SWITCH_ON_IO`。发起I/O立刻切换进程，行为与Q3完全一致。PID0发起I/O后切换至PID1，PID1的4条CPU指令在PID0 I/O阻塞期间全部执行完成，实现时间重叠。总时间7 tick。和Q4对比，相同进程，调度策略不同带来巨大性能差异。
Verified result / 验证结果:
Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%)
- Analysis / 分析:
## Q6
- Prediction / 预测:
Total Time: 35 ticks，CPU Busy: 21，CPU Utilization: 60.00%
- Reasoning / 理由:
配置 `-l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -I IO_RUN_LATER`。PID0执行3次I/O；PID1/PID2/PID3各5条CPU指令。
`SWITCH_ON_IO`：PID0每次执行`RUN:io`就切换；
`IO_RUN_LATER`：I/O完成后PID0变为READY，加入就绪队列末尾，**不抢占CPU**；
I/O设备支持并行，可同时处理多个I/O请求；CPU总工作量：PID0(3×2=6) + PID1+PID2+PID3(15) =21。因为PID0 I/O完成后需要排队等待CPU，总时间大于CPU Busy的数值。
Verified result / 验证结果:
Stats: Total Time 31
Stats: CPU Busy 21 (67.74%)
Stats: IO Busy  15 (48.39%)
- Analysis / 分析:
## Q7
- Prediction / 预测:
Total Time: 31 ticks，CPU Busy: 21，CPU Utilization: 67.74%
- ReasonVing / 理由:
配置 `-l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -I IO_RUN_IMMEDIATE`。
`IO_RUN_IMMEDIATE`：I/O一旦完成，PID0立刻抢占CPU运行，不用排在就绪队列末尾。PID0可以快速发起下一轮I/O，减少I/O设备空闲时间。CPU总工作量仍然21 tick；对比Q6总时间会更小，系统整体吞吐提升；代价是抢占会打断CPU密集进程的执行。
Verified result / 验证结果:
Stats: Total Time 21
Stats: CPU Busy 21 (100.00%)
Stats: IO Busy  15 (71.43%)
- Analysis / 分析:
## Q8
- Prediction / 预测:> `-l 3:50,3:50`；每条指令50%概率CPU / I/O；指令序列由seed=1,2,3确定，无固定时间。
预期规律：
1. `SWITCH_ON_END`策略总时间普遍更长，I/O阻塞不切换，CPU更容易空闲；
2. `IO_RUN_IMMEDIATE`I/O完成抢占CPU，I/O设备利用率更高；
3. 不同种子生成不同指令序列，调度策略带来的性能影响大小会随之变化。
- Reasoning / 理由:
50%随机概率生成指令，每个seed对应一套固定指令序列。固定指令序列下修改`‑S`、`‑I`参数，观察调度策略如何改变总时间、CPU利用率、IO忙碌率。`SWITCH_ON_END`放弃I/O等待时间并行机会；`IO_RUN_IMMEDIATE`有利于I/O密集任务，维持I/O设备忙碌。
- Verified result / 验证结果:
seed‑1 default: Total Time: 15, CPU Busy: 8 (53.33%), IO Busy: 10 (66.67%)
seed‑1 IO_RUN_IMMEDIATE: Total Time: 15, CPU Busy: 8 (53.33%), IO Busy: 10 (66.67%)
seed‑1 SWITCH_ON_END: Total Time: 18, CPU Busy: 8 (44.44%), IO Busy: 10 (55.56%)

seed‑2 default: Total Time: 16, CPU Busy: 10 (62.50%), IO Busy: 14 (87.50%)
seed‑2 IO_RUN_IMMEDIATE: Total Time: 16, CPU Busy: 10 (62.50%), IO Busy: 14 (87.50%)
seed‑2 SWITCH_ON_END: Total Time: 30, CPU Busy: 10 (33.33%), IO Busy: 20 (66.67%)

seed‑3 default: Total Time: 18, CPU Busy: 9 (50.00%), IO Busy: 11 (61.11%)
seed‑3 IO_RUN_IMMEDIATE: Total Time: 17, CPU Busy: 9 (52.94%), IO Busy: 11 (64.71%)
seed‑3 SWITCH_ON_END: Total Time: 24, CPU Busy: 9 (37.50%), IO Busy: 15 (62.50%)

- Analysis / 分析: