<template>
  <div>
    <div v-if="state == 'rejected'">
      <StateBadge :state="state" class="fs-6"></StateBadge>
    </div>
    <div v-else class="row align-items-center justify-content-around m-0 flex-nowrap">
      <template v-for="(value, index) of states">
        <template v-if="value !== 'rejected'">
          <div class="col-auto p-0" :key="value">
            <StateBadge :state="value" :class="state === value ? 'fs-6' : 'fw-normal'"></StateBadge>
          </div>
          <div v-if="value !== 'refunded' && value !== 'completed'" class="col p-0" :key="value">
            <hr
              :style="
                'background: linear-gradient(to right, ' +
                APP_DATA?.displaySettings.stateColors[value].color +
                ', ' +
                APP_DATA?.displaySettings.stateColors[states[index + 1]].color +
                '); height: 5px; border: 0px'
              " />
          </div>
        </template>
      </template>
    </div>
  </div>
</template>

<script lang="ts">
import { AnyState } from '@/../../common/types.js'
import APP_LOADER from '@/appData.js'
import StateBadge from '@/components/elements/StateBadge.vue'
import { PropType, defineComponent } from 'vue'

export default defineComponent({
  data() {
    return {
      APP_DATA: APP_LOADER.data
    }
  },
  name: 'StatePipeline',
  components: { StateBadge },
  props: {
    state: { type: String as PropType<AnyState>, required: true },
    states: { type: Array as PropType<readonly AnyState[]>, required: true }
  },
  mounted() {},
  async created() {
    await APP_LOADER.loadData()
  }
})
</script>

<style></style>
