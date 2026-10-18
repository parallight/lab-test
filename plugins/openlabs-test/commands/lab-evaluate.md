---
name: lab-evaluate
description: 项目2 CPU自测或正式GPU评测 / 旧Lab评测 / 查看结果
---

<!-- AUTO-GENERATED from commands-src/lab-evaluate.md — do not edit. Run `pnpm gen:commands`. -->

## Project-2（先判断，和旧 Lab 分开）

`evaluate project-2 --soft` 或 `/lab-evaluate project-2 --soft`：调用 `lab_evaluate`（或 `evaluate`），传 `project: "project-2", mode: "soft"`。命令可带 `--repo owner/repo --commit <完整40位SHA> --ref main`，分别映射工具的 `repository`、`commit_sha`、`ref`；省略时插件只读当前 Git origin/HEAD/branch。插件不自动提交、push、安装工作流，也不执行本地 setup.sh 或任何仓库脚本。

soft 通过已有 GitHub CLI 登录派发仓库默认分支里的 `.github/workflows/llm-soft.yml`，输入固定 commit SHA 和唯一 request_id，在 GitHub Actions 上跑 CPU 检查。工作流也可在 push 时自动执行。仓库没有该工作流时，给出工具返回的固定测试站下载地址和任务书指导，让用户自行安装；不要擅自写入仓库。GitHub Actions 可能使用账户自身的执行分钟额度。soft 不申请平台 GPU、不扣平台 GPU 实验时间，结果是可修改的公开自测证据，**不是正式分数，也不证明吞吐/TTFT/TPOT或GPU优化达标**。

`evaluate project-2 --formal`：仅在用户明确要求正式评测后调用同工具，传 `project: "project-2", mode: "formal"` 和仓库/完整提交 SHA。复用 `lab-login` 的登录态，由测试站验证本人 cohort-2 权限、配额和预算，再从固定 GitHub 提交建立独立 GPU 环境。这会使用正式评测次数和 GPU 预算；不因 soft 失败或通过而自动发起。正式评测只接受 commit，不接受 branch ref。网络超时/未知结果保留返回的 request_id，查询或人工核实；不要自动换新 request_id 重提。

只说 `evaluate project-2` 没有模式时，询问 CPU 自测还是正式 GPU 评测，**不可默认 formal**。不要把 project-2 当作旧 task，也不要设置 HYPER_LAB_CONFIG、复制网页 cookie、使用 ev_/p12_ 评测配置或更换其他账号凭据。Project-2 当前固定走测试站，客户端不能指定任意后端 URL。

`/lab-evaluate project-2 result <job_id>`：调用 `lab_evaluate_result`（或 `evaluate_result`），传 `project: "project-2", job_id`。soft ID是 `github:owner/repo:runID` 或工具返回的 `github-pending:...` 标识；正式ID是平台UUID。一次查询只读一次状态，不等待循环、不重新提交。没有唯一匹配的GitHub运行时明确说尚未定位，不能拿另一提交/另一用户的报告替代。结果完整展示各项通过情况、失败原因和证据来源；公开 CPU artifact 不得称为平台认证成绩。

Project-2 的公开参考实现可以用来解释和对照，可调用 `lab_assistant(project="project-2", question="...")` 获取带版本的公开资料和本人正式评测。以下旧 Lab 的“禁止参考解”规则只适用于旧 Lab，不应阻止解释已经公开的项目2参考源码。不要提供隐藏评分器、平台凭据或他人记录。

## 旧 Lab（保持原行为）

学员想让当前这个 lab 的 agent 代码接受一次云端评测(挂真实工具跑固定场景、自动判定通过率),或者想查看之前提交的一次评测跑得怎么样了。

从 `$ARGUMENTS` 判断学员想干什么:

- 参数长得像任务号(如 `t3`、`t12`,即 `t` 后跟数字)→ 学员要**提交**这个 task 去评测。调 `lab_evaluate`(`task` = 那个任务号)。如果学员另外提了要多跑几次看稳定性,传 `k`(1-5);没提就不传,交给后端的默认值。
- 参数以 `result` 开头(如 `result <job_id>`),或者参数本身就是一串 job id/uuid → 学员要**查看**某次评测的结果。取出其中的 job_id 调 `lab_evaluate_result`。
- 没给参数、或看不出是哪种 → 别瞎猜,直接问学员:是要提交哪个 task 去评测,还是要查看之前提交的评测结果(查后者要问清楚 job_id;记不住的话,重新提交一次拿新 job_id 也行)。

**提交后**(`lab_evaluate` 返回)照工具给的 `[NOW DO THIS]` 指示做:告诉学员任务已提交、云端约需 2-6 分钟跑完,ta 可以先继续手头的事,不用干等。**不要在这里自己反复调用 lab_evaluate_result 去轮询等结果** —— 把节奏交还给学员,等 ta 主动用 `/lab-evaluate result <job_id>` 再来查。

**查看结果**(`lab_evaluate_result` 返回)时:

- 还在排队/跑中 → 把简短进度告诉学员,提示过几分钟再来看,不用一直问。
- 出错 → 把中文错误原因原样转达给学员。
- 跑完了 → **场景矩阵表原样完整展示给学员**(通过率 x/k、失败原因摘要这一列都保留,不要精简或改写表格)。然后按工具给的 `[NOW DO THIS]` 逐场景口头解读:哪些场景全过、哪些没过;失败原因里把「agent 报错」(agent 自己执行时出错、工具调用失败)和纯粹的断言失败(agent 跑完了但结果不对)分开说清楚,别混为一谈;把每个失败场景关联回对应的知识点。最后给**一句话结论**(整体过了多少、卡在哪个知识点)+ **建议的下一步**(该改哪块代码,改完要不要再跑一次 /lab-evaluate)。**绝不能**透露参考解法的具体内容 —— 只讲学员自己代码错在哪、原理上该往哪个方向想,让学员自己动手改。

如果工具提示「还没登录」,引导学员先 /lab-login。提示「没有进行中的 lab」,引导 /lab 选一个开始。提示「尚未付费开通」,如实告知评测是付费功能。
