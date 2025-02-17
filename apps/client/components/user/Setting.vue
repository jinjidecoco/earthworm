<template>
  <div class="space-y-4 min-w-max">
    <section class="space-y-2">
      <h2 class="text-lg">快捷键设置</h2>
      <table class="table">
        <thead>
          <tr>
            <th class="text-base">命令</th>
            <th class="text-base">键绑定</th>
            <th class="w-[300px] text-base text-center">操作</th>
          </tr>
        </thead>
        <tbody>
          <tr class="hover">
            <td class="label-text">播放发音</td>
            <td>{{ shortcutKeys.sound }}</td>
            <td class="text-center">
              <button
                class="btn btn-sm btn-outline btn-secondary"
                @click="handleEdit('sound')"
              >
                编辑
              </button>
            </td>
          </tr>
          <tr class="hover">
            <td class="label-text">切换答题/答案页面</td>
            <td>{{ shortcutKeys.answer }}</td>
            <td class="text-center">
              <button
                class="btn btn-sm btn-outline btn-secondary"
                @click="handleEdit('answer')"
              >
                编辑
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </section>

    <section class="space-y-2">
      <h2 class="text-lg">声音设置</h2>
      <table class="table">
        <tbody>
          <tr class="hover">
            <td class="label-text">开启键盘打字音效</td>
            <td class="w-[300px] text-center">
              <input
                type="checkbox"
                class="toggle toggle-secondary"
                :checked="keyboardSound"
                @change="toggleKeyboardSound"
              />
            </td>
          </tr>
          <tr class="hover">
            <td class="label-text">自动播放声音（答案页面）</td>
            <td class="w-[300px] text-center">
              <input
                type="checkbox"
                class="toggle toggle-secondary"
                :checked="autoPlaySound"
                @change="toggleAutoPlaySound"
              />
            </td>
          </tr>
          <tr class="hover">
            <td class="label-text">切换口音</td>
            <td class="w-[300px] text-center">
              <div class="join">
                <input
                  v-for="lang in getPronunciationOptions()"
                  class="join-item btn btn-sm"
                  type="radio"
                  name="options"
                  :value="lang.value"
                  :aria-label="lang.label"
                  :checked="pronunciation === lang.value"
                  @change="togglePronunciation(lang.value as PronunciationType)"
                />
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </section>

    <section class="space-y-2">
      <h2 class="text-lg">答题设置</h2>
      <table class="table">
        <tbody>
          <tr class="hover">
            <td class="label-text">显示每个单词长度</td>
            <td class="w-[300px] text-center">
              <input
                type="checkbox"
                class="toggle toggle-secondary"
                :checked="showWordsWidth"
                @change="toggleAutoWordsWidth"
              />
            </td>
          </tr>
          <tr class="hover">
            <td class="label-text">开启空格提交答案</td>
            <td class="w-[300px] text-center">
              <input
                type="checkbox"
                class="toggle toggle-secondary"
                :checked="useSpace"
                @change="toggleUseSpaceSubmitAnswer"
              />
            </td>
          </tr>
        </tbody>
      </table>
    </section>
  </div>

  <dialog
    class="modal mt-[-8vh]"
    :open="showModal"
  >
    <div
      ref="dialogBoxRef"
      class="modal-box max-w-[48rem] min-h-[156px]"
    >
      <h3 class="mb-4 text-center text-base font-bold text-fuchsia-500">
        请先按下单键/组合键，通过回车键（Enter ⏎）来设置
      </h3>
      <div class="h-8 leading-8 border border-solid border-fuchsia-500 rounded text-center">
        {{ shortcutKeyStr }}
      </div>
      <div class="text-center mt-2 text-xs">
        {{ shortcutKeyTip }}
      </div>
    </div>
  </dialog>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import { PronunciationType, usePronunciation } from "~/composables/user/pronunciation";
import { useShortcutKeyMode } from "~/composables/user/shortcutKey";
import {
  useAutoPronunciation,
  useKeyboardSound,
} from "~/composables/user/sound";
import { useSpaceSubmitAnswer } from "~/composables/user/submitKey";
import { useShowWordsWidth } from "~/composables/user/words";

const dialogBoxRef = ref<HTMLElement | null>(null);
const { keyboardSound, toggleKeyboardSound } = useKeyboardSound();
const { autoPlaySound, toggleAutoPlaySound } = useAutoPronunciation();
const {
  pronunciation,
  // 发音配置列表
  getPronunciationOptions,
  togglePronunciation,
} = usePronunciation();
const { showWordsWidth, toggleAutoWordsWidth } = useShowWordsWidth();
const { useSpace, toggleUseSpaceSubmitAnswer } = useSpaceSubmitAnswer();
const {
  showModal,
  shortcutKeys,
  shortcutKeyStr,
  shortcutKeyTip,
  handleEdit,
  handleCloseDialog,
  handleKeydown,
} = useShortcutKeyMode();

function pointDialogOutside(e: MouseEvent) {
  if (!showModal.value) return;
  if (!dialogBoxRef.value?.contains(e.target as Node)) {
    handleCloseDialog();
  }
}

onMounted(() => {
  document.addEventListener("mouseup", pointDialogOutside);
  document.addEventListener("keydown", handleKeydown);
});
onUnmounted(() => {
  document.removeEventListener("mouseup", pointDialogOutside);
  document.removeEventListener("keydown", handleKeydown);
});
</script>

<style scoped>
.btn-outline.btn-secondary:hover,
.toggle-secondary:checked,
.btn:is(input[type="radio"]:checked) {
  @apply text-[#ffffff] border-fuchsia-500 bg-fuchsia-500;
}

.btn-outline.btn-secondary {
  @apply text-fuchsia-500 outline-fuchsia-500;
}
</style>
