<template>
  <div
    :class="`status py-[10px] px-[16px] flex items-center justify-center gap-5 rounded-full border-2 bg-[${statusInfo.bg_color}] border-[${statusInfo.color}] text-[${statusInfo.color}]`"
  >
    <Icons :name="statusInfo.icon_name" :color="statusInfo.color" />

    <span>{{ statusInfo.text }}</span>
    <span>{{ statusInfo.count }}</span>
  </div>
</template>

<script setup>
import Icons from '../components/Icons.vue'


const props = defineProps(['statusInfo'])
</script>

<style lang="scss" scoped></style>
