<template>
  <section class="blank-page">
    <button class="back-btn" type="button" @click="goHome">返回首页</button>
    <div class="panel cards">
      <h1 class="title">{{ pageTitle }}</h1>
      <div v-if="pageContent" class="content" v-html="pageContent" />
      <div v-else class="content empty">
        <p>内容正在整理中。</p>
      </div>
    </div>
  </section>
</template>

<script setup>
import { mainStore } from "@/store";

const store = mainStore();

const pageTitle = computed(() => store.activePageTitle || "页面");

const base = import.meta.env.BASE_URL || "/";

const pageContentMap = {
  intro:
    "<h3>关于我</h3><p>你好！我是刘一骏，目前就读于南方科技大学计算机科学专业，大三。我对强化学习（RL）、量化交易和人工智能有着浓厚的兴趣。我的研究方向主要是将机器学习方法，尤其是强化学习算法，应用于金融市场预测，从而判断所选股票的走势。</p>",
  resume: `
    <h3>个人简历</h3>
    <p>下面是我的简历预览与下载入口。</p>
    <div style="margin:16px 0 20px; text-align:center;">
      <a href="${base}files/resume.pdf" target="_blank" rel="noopener noreferrer" title="点击查看简历原文件">
        <img src="${base}images/resume.pdf.png" alt="简历缩略图" style="max-width:100%; width:min(680px,100%); height:auto; border-radius:10px; box-shadow:0 10px 30px rgb(0 0 0 / 18%);" />
      </a>
    </div>
    <div style="display:flex; gap:12px; flex-wrap:wrap; align-items:center;">
      <a href="${base}files/resume.pdf" target="_blank" rel="noopener noreferrer" style="display:inline-block; padding:10px 16px; border-radius:10px; background:rgb(255 255 255 / 14%); color:#fff; text-decoration:none;">在线查看</a>
      <a href="${base}files/resume.pdf" download="刘一骏-简历.pdf" style="display:inline-block; padding:10px 16px; border-radius:10px; background:rgb(255 255 255 / 22%); color:#fff; text-decoration:none;">下载简历</a>
    </div>
  `,
  blog: `
    <h3>建站记录</h3>
    <p>今天我创建了这个个人主页，用来整理我的简介、项目、简历与博客内容，也作为我个人作品与学习记录的展示页面。</p>
    <p>后续我会继续更新项目介绍、研究笔记和一些日常记录，让这个主页逐步变成一个更完整的个人空间。</p>
  `,
  projects: `
    <h3>漂移场流程图</h3>
    <div style="margin:16px 0 20px; text-align:center;">
      <img src="${base}images/drifting-model.png" alt="Drifting Model 流程图" style="max-width:100%; height:auto; border-radius:10px; box-shadow:0 10px 30px rgb(0 0 0 / 18%);" />
    </div>
    <p>训练阶段，生成器将随机噪声映射为生成样本，漂移场同时结合真实数据分布和当前生成样本，计算出吸引-排斥的漂移向量；通过停止梯度（stop-gradient）构造固定目标，迫使生成器输出向该目标靠拢，从而间接最小化漂移场范数，推动生成分布逼近真实分布。</p>
    <p>推理时，训练好的生成器只需一次前向传播即可从噪声直接生成符合真实分布的新样本，再经由解码器转换为最终的因子表达式。</p>

    <h3 style="margin-top:28px;">基于进化策略的 QFR 因子生成流程</h3>
    <div style="margin:16px 0 20px; text-align:center;">
      <img src="${base}images/微信图片_20260511151252_39_55.png" alt="基于进化策略的 QFR 因子生成流程图" style="max-width:100%; height:auto; border-radius:10px; box-shadow:0 10px 30px rgb(0 0 0 / 18%);" />
    </div>
    <p>本图展示了基于进化策略（Evolution Strategies, ES）训练 QFR 策略网络、进而生成股票因子池的完整流程。流程首先初始化随机种子、目标表达式、训练/测试数据、AlphaPool、AlphaEnv 以及 QFR 策略网络。</p>
    <p>在每一代（generation）中，算法会对策略参数施加噪声扰动（支持镜像采样以减少方差），生成多个候选参数向量；每个候选参数被载入策略网络后在环境中运行 eval_steps 步，累积原始 IC 作为原始奖励，并依据 Eq.(11) 计算 IR 塑形奖励（惩罚过低的 IR 值）。</p>
    <p>完成所有候选的评估后，采用 centered ranks 或标准化进行适应度塑形，估计自然梯度，并更新参数。每隔若干代，流程会在测试集上评估当前因子池的 IC、rank IC 以及 long-short 收益等指标。</p>
    <p>所有世代结束后，加载最终策略参数进行一次最终评估，并将最终的因子池保存为 final_pool.json 文件。</p>
    `,
};

const pageContent = computed(() => pageContentMap[store.activePage] || "");

const goHome = () => {
  window.location.hash = "#/";
  store.goHome();
};
</script>

<style lang="scss" scoped>
.blank-page {
  position: fixed;
  inset: 0;
  z-index: 3;
  display: flex;
  align-items: flex-start;
  justify-content: center;
  padding: 32px 20px;
  background: transparent;
  backdrop-filter: none;
  overflow-y: auto;

  .back-btn {
    position: fixed;
    top: 24px;
    left: 24px;
    padding: 10px 16px;
    border: 0;
    border-radius: 999px;
    color: #fff;
    background: rgb(255 255 255 / 12%);
    cursor: pointer;
    transition: transform 0.2s, background 0.2s;

    &:hover {
      background: rgb(255 255 255 / 18%);
      transform: translateY(-1px);
    }
  }

  .panel {
    width: min(860px, 100%);
    max-height: calc(100vh - 96px);
    overflow-y: auto;
    padding: 36px 32px;
    color: #fff;
    background: rgb(0 0 0 / 24%);
    backdrop-filter: blur(16px);
    border-radius: 18px;
    box-shadow: 0 18px 60px rgb(0 0 0 / 28%);

    .title {
      margin: 0 0 18px;
      font-size: clamp(1.8rem, 4vw, 3.2rem);
      letter-spacing: 0.06em;
      text-shadow: 0 8px 30px rgb(0 0 0 / 35%);
      text-align: left;
    }

    .content {
      line-height: 1.9;
      font-size: 1rem;
      color: rgb(255 255 255 / 92%);

      :deep(h3) {
        margin: 0 0 12px;
        font-size: 1.2rem;
        color: #fff;
      }

      :deep(p) {
        margin: 0;
      }

      &.empty {
        min-height: 120px;
        display: flex;
        align-items: center;
        justify-content: center;
        color: rgb(255 255 255 / 70%);
      }
    }
  }
}

@media (max-width: 720px) {
  .blank-page {
    padding: 18px 14px;
    align-items: flex-start;

    .panel {
      padding: 24px 18px;
      border-radius: 14px;
      max-height: calc(100vh - 64px);
    }
  }
}
</style>
