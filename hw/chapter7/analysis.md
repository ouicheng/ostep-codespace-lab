# Chapter7 CPU Scheduling Homework
## Q1
- Prediction / 预测:
- Reasoning / 理由:
- Verified result / 验证结果:
FIFO: Avg Response: 200.00, Avg Turnaround: 400.00
SJF: Avg Response: 200.00, Avg Turnaround: 400.00
- Analysis / 分析:
预测结果和模拟器输出完全吻合。3个作业运行时长完全相同，FIFO与SJF执行顺序完全一样。FIFO按照到达顺序依次执行；SJF挑选最短作业，但所有作业长度一致，调度顺序不变。平均响应时间、平均周转时间两个指标完全相等。

## Q2
- Prediction / 预测:
- Reasoning / 理由:
- Verified result / 验证结果:
FIFO: Avg Response:133.33, Avg Turnaround:333.33
SJF: Avg Response:133.33, Avg Turnaround:333.33
- Analysis / 分析:
预测结果与模拟器结果一致。作业到达顺序就是作业长度由短到长（100，200，300）。FIFO按到达顺序执行，SJF选择最短作业执行，两者执行序列完全一样，因此性能指标没有区别。只有当长作业先到达、短作业后到达时，SJF才会体现出周转时间优势。

## Q3
- Prediction / 预测:
- Reasoning / 理由:
- Verified result / 验证结果:
RR(quantum=1): Avg Response: 1.00, Avg Turnaround: 465.67
- Analysis / 分析:
RR时间片=1，每个作业轮流执行1个时间单位。响应时间表现非常优秀，3个作业很快都能得到第一次调度，平均响应时间仅1.00。但是大量上下文切换拉长了整体完成时间，平均周转时间大幅变差。体现调度算法经典权衡：RR优化**响应时间（交互体验）**，牺牲**周转时间（批处理效率）**。

## Q4
- Prediction / 预测:
- Reasoning / 理由:
- Verified result / 验证结果: N/A（理论分析题，无模拟器输出）
- Analysis / 分析:
当**作业到达顺序与作业运行时长从小到大的顺序完全一致**时，SJF和FIFO会执行完全相同的序列，周转时间相等。
例如作业按短→中→长依次到达。此时FIFO先来先执行，SJF每次也选当前最短，调度序列一致。如果长作业最先到达，则两者行为就会出现巨大差距，FIFO会出现车队效应。

## Q5
- Prediction / 预测:
- Reasoning / 理由:
- Verified result / 验证结果: N/A（理论分析题，无模拟器输出）
- Analysis / 分析:
所有作业**同时到达，且时间片quantum大于等于最长作业的运行时间**。
此时RR每个作业拿到完整时间片就直接运行到结束，行为退化为非抢占，执行顺序等价于FIFO；如果同时作业恰好是短作业排在前面，则RR行为就等价SJF，响应时间就会和SJF完全一样。简单理解：时间片足够大，RR就不再切换，变成批处理调度。

## Q6
- Prediction / 预测:
- Reasoning / 理由:
- Verified result / 验证结果: N/A（理论分析题，无模拟器输出）
- Analysis / 分析:
SJF是非抢占式调度。第一个作业一旦开始运行就会运行至结束。如果第一个作业长度不断增大，后面所有作业第一次被调度的时刻就会被不断推迟，**平均响应时间会随之变大**。长作业先到会造成后面所有作业等待，也就是车队效应。

## Q7
- Prediction / 预测:
- Reasoning / 理由:
- Verified result / 验证结果: N/A（理论分析题，无模拟器输出）
- Analysis / 分析:
RR：时间片quantum越大，响应时间会变差。时间片越大，作业要等待更久才能轮到自己第一次运行。
N个作业RR最坏情况响应时间公式：`T_response_worst = (N‑1) * quantum`。
最坏情形：一个作业刚好在其他N‑1个作业全部刚获得时间片之后到达，它需要等待其余N‑1个全部跑完一个完整时间片，才可以获得第一次CPU运行。
时间片调大，响应时间变差；时间片调小，响应时间变好，但上下文切换开销上升，周转时间恶化。
