<script setup lang="ts">
import {LLM} from "@/utils";
import {Request} from "@/types";
import {useModel} from "@/hooks";
import {configStore} from "@/stores";
import {storeToRefs} from "pinia";
import {reactive, toRefs} from "vue";
import Modal from "@/components/Modal.vue";
import MInput from "@/components/MInput.vue";

const props = defineProps<{
  modelValue: boolean
}>()

const emits = defineEmits(["update:modelValue"])

const {modelValue} = props

const state = useModel(modelValue, props, "modelValue", emits, "update:modelValue")

const config = configStore();

const {changeUrl, changePat, changeBotId} = config
const {coze} = storeToRefs(config)
const {url: _url, pat: _pat, botId: _botId} = toRefs(coze.value)

const data = reactive<Request>({
  url: _url.value,
  pat: _pat.value,
  botId: _botId.value,
})

const {url, pat, botId} = toRefs(data)

const modalConfirm = () => {
  if (!confirm("确认修改吗？")) return
  changeUrl(url.value)
  changePat(pat.value)
  changeBotId(botId.value)
  LLM.initClient()
}
</script>

<template>
  <div class="chat-config">
    <Modal @confirm="modalConfirm" v-model="state" title="自定义配置项" :closeOnClickModal="false">
      <ul>
        <li>
          baseUrl:
          <MInput v-model="url"/>
        </li>
        <li>
          botId:
          <MInput v-model="pat"/>
        </li>
        <li>
          pat:
          <MInput v-model="botId"/>
        </li>
      </ul>
    </Modal>
  </div>
</template>

<style scoped lang="scss">
@use "@/assets/styles/ChatConfig.module";
</style>