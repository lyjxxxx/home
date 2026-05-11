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

const pageContentMap = {
  intro:
    "<h3>关于我</h3><p>你好！我是刘一骏，目前就读于南方科技大学计算机科学专业，大三。我对强化学习（RL）、量化交易和人工智能有着浓厚的兴趣。我的研究方向主要是将机器学习方法，尤其是强化学习算法，应用于金融市场预测，从而判断所选股票的走势。</p>",
    projects: `
      <h3>Drifting Model — 训练与推理流程图</h3>
    <div style="margin:16px 0; text-align:center;">
      <img src="/images/drifting-model.png" alt="Drifting Model 流程图" style="max-width:100%; height:auto; border-radius:8px;" />
    </div>
      <p>训练阶段，生成器将随机噪声映射为生成样本，漂移场同时结合真实数据分布和当前生成样本，计算出吸引-排斥的漂移向量；通过停止梯度（stop‑gradient）构造固定目标，迫使生成器输出向该目标靠拢，从而间接最小化漂移场范数，推动生成分布逼近真实分布。</p>
      <p>推理时，训练好的生成器只需一次前向传播即可从噪声直接生成符合真实分布的新样本，再经由解码器转换为最终的因子表达式。</p>
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
  align-items: center;
  justify-content: center;
  padding: 32px 20px;
  background: transparent;
  backdrop-filter: none;

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

    .panel {
      padding: 24px 18px;
      border-radius: 14px;
    }
  }
}
</style>
