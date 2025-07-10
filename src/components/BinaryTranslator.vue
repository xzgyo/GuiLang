<template>
  <div class="page-wrapper">
    <nav class="navbar navbar-dark fixed-top px-3" :style="{ backgroundColor: themeColor }">
      <div class="container-fluid d-flex flex-wrap align-items-center gap-3">
        <span class="navbar-brand mb-0 h1">鬼话翻译器</span>
        <div class="d-flex align-items-center gap-2">
          <button class="btn btn-light" style="padding: 4px 8px" type="button" data-bs-toggle="offcanvas" data-bs-target="#settings-panel" aria-controls="staticBackdrop">
            <i class="bi bi-gear"></i> 设置
          </button>
          <a class="text-white" style="text-decoration: none !important;">Made by Xzgyo at 中国上海</a>
        </div>
        <!--input
          type="color"
          class="form-control form-control-color ms-auto"
          v-model="themeColor"
          title="Select Theme Color"
          style="width: 3rem"
        /-->
      </div>
    </nav>
    <div class="offcanvas offcanvas-end" data-bs-scroll="true" tabindex="-1" id="settings-panel" aria-labelledby="staticBackdropLabel">
      <div class="offcanvas-header">
        <h5 class="offcanvas-title" id="staticBackdropLabel"><i class="bi bi-gear"></i> 设置</h5>
        <button type="button" class="btn-close" data-bs-dismiss="offcanvas" aria-label="Close"></button>
      </div>
      <div class="offcanvas-body" id="settings-page">
        <div class="card" style="width: 100%;">
          <div class="card-header py-2 px-3">输出设置</div>
          <div class="d-flex align-items-center gap-2 flex-wrap" style="padding: .5rem 1rem .5rem;">
            <label class="text-black mb-0">0=</label>
            <input v-model="zeroChar" maxlength="1" class="form-control form-control-sm" style="width: 50px;" />
          </div>
          <div class="d-flex align-items-center gap-2 flex-wrap" style="padding: .5rem 1rem .5rem;">
            <label class="text-black mb-0">1=</label>
            <input v-model="oneChar" maxlength="1" class="form-control form-control-sm" style="width: 50px;" />
          </div>
        </div>
        <div class="card" style="width: 100%">
          <div class="card-header py-2 px-3">翻译模式</div>
          <div class="switch-box" style="margin: 8px;width: calc(100% - 16px);justify-content: space-between;">
            <a>从鬼话解码</a>
            <div class="switch-btn">
              <input class="switch-input" type="checkbox" id="reverseMode" v-model="reverseMode">
              <label class="switch-label" for="reverseMode"></label>
            </div>
          </div>
      </div>
    </div>
  </div>
    <div class="d-flex justify-content-center main-content">
      <div class="container" style="height: max-content;">
        <div id="guilang-translator-main" class="row" style="margin: 10px 0 0 0;">
          <div class="col-md-6 mb-3" style="height: 100%">
            <NBTextBlock
              title="说人话"
              v-model="inputText"
              placeholder="请输入任意内容"
              :readonly="reverseMode"
              style="height: max-content"
            />
          </div>
          <div class="col-md-6 mb-3" style="height: 100%">
            <NBTextBlock
              title="我就说鬼话"
              v-model="outputText"
              :readonly="!reverseMode"
              style="height: max-content"
            />
          </div>
        </div>
      </div>
    </div>
    <div class="footer">
      <hr style="margin: 1rem 14px;">
      <p style="padding: 0 20px;">Made By Xzgyo at 中国上海</p>
    </div>
  </div>
</template>
<script setup lang="ts">
import { ref, computed, watchEffect } from 'vue';
import NBTextBlock from './NBTextBlock.vue';

const themeColor = ref<string>('#0d6efd');
const zeroChar = ref<string>('九');
const oneChar = ref<string>('一');
const inputText = ref('');
const outputText = ref('');
const reverseMode = ref(false);

const encodedHuman = computed(() => {
  const encoder = new TextEncoder();
  const bytes = encoder.encode(inputText.value);
  const bits: number[] = [];
  for (const byte of bytes) {
    const binaryStr = byte.toString(2).padStart(8, '0');
    for (const bit of binaryStr) {
      bits.push(bit === '1' ? 1 : 0);
    }
  }
  return bits.map(c => c.toString().replace(/0/g, zeroChar.value).replace(/1/g, oneChar.value)).join('');
});

const decodedGui = computed(() => {
  const binary = outputText.value
    .split('')
    .map(c => (c === zeroChar.value ? '0' : c === oneChar.value ? '1' : ''))
    .join('');
  const charsCode: number[] = []
  for (let i = 0; i < binary.length; i += 8) {
    const byte = binary.slice(i, i + 8);
    if (byte.length === 8) {
      charsCode.push(parseInt(byte, 2));
    }
  }
  var result = new TextDecoder('utf-8').decode(new Uint8Array(charsCode));
  return result;
});

watchEffect(() => {
  if (!reverseMode.value) {
    outputText.value = encodedHuman.value
  } else {
    inputText.value = decodedGui.value
  }
});
</script>

<style scoped>
textarea {
  font-family: monospace;
  word-break: break-word;
  resize: none;
}

.card {
  border-radius: 16px;
  border-width: 2px;
  --bs-card-inner-border-radius: 10px !important;
}

#settings-page > *:not(:first-child) {
  margin-top: 16px;
}

#guilang-translator-main > * {
  height: 450px !important;
  max-height: calc(100dvh - 78px) !important;
}

.page-wrapper {
  height: 100vh;
  display: flex;
  flex-direction: column;
  overflow-y: auto;
}

.main-content {
  flex: 1;
  padding-top: 50px;
}

.footer {
  margin-top: auto;
}
</style>
