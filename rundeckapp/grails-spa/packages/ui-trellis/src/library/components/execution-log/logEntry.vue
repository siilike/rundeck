<template>
    <div class="log-line">
        <span class="gutter">{{entry.node}}</span>
        <span v-if="entry.loghtml" v-html="entry.loghtml"/>
        <span v-if="!entry.loghtml">{{entry.log}}</span>
    </div>
</template>

<script lang="ts">
import {defineComponent} from 'vue'

export default defineComponent({
    props: ['entry']
})
</script>

<style lang="scss" scoped>
.log-line {
  display: block;
  width: 100%;
  font-family: monospace;
}

.gutter {
  user-select: none;
}
</style>