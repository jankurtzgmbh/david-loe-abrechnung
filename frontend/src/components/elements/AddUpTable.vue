<template>
  <table class="table align-bottom">
    <tbody>
      <tr v-if="progress !== undefined">
        <th>{{ t('labels.progress') }}</th>
        <td class="text-end">
          <ProgressCircle :progress="progress"></ProgressCircle>
        </td>
      </tr>
      <tr v-for="row of addUpTableData">
        <th>
          {{ t(row[0]) }}
          <small class="fw-normal" v-if="row[0] === 'labels.lumpSums' && claimSpouseRefund">
            <br />
            {{ t('labels.includingSpouseRefund') }}
          </small>
        </th>
        <template v-for="(col, index) of row">
          <td v-if="index !== 0" class="text-end">
            {{ col }}
            <small v-if="row[0] === 'labels.advance' && showAdvanceOverflow && addUp[index - 1].advanceOverflow">
              <br />
              {{ `(${$formatter.baseCurrency(addUp[index - 1].advance.amount - addUp[index - 1].total.amount)} ${t('labels.left')})` }}
            </small>
          </td>
        </template>
      </tr>
      <tr v-if="addUp.length > 1">
        <th>{{ t('labels.totalBalance') }}</th>
        <td :colspan="addUp.length" class="text-end">{{ $formatter.baseCurrency(getTotalBalance(addUp)) }}</td>
      </tr>
      <tr v-if="refundSum">
        <th :colspan="addUp.length">{{ t('labels.refundSum') }}</th>
        <td class="text-end">{{ $formatter.money(refundSum) }}</td>
      </tr>
      <tr v-if="project.budget && project.budget.amount">
        <td>
          <small>{{ t('labels.project') }}</small>
        </td>
        <td class="text-end">
          <small>{{ $formatter.money(project.balance) + ' von ' + $formatter.money(project.budget) }}</small>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script setup lang="ts">
import { getAddUpTableData, getTotalBalance } from '@/../../common/scripts'
import { AddUp, Money, Project } from '@/../../common/types.js'
import ProgressCircle from '@/components/elements/ProgressCircle.vue'
import { formatter } from '@/formatter'
import { PropType, computed, ref, watch } from 'vue'
import { useI18n } from 'vue-i18n'

const { t } = useI18n()

const props = defineProps({
  addUp: { type: Array as PropType<AddUp[]>, required: true },
  claimSpouseRefund: { type: Boolean as PropType<boolean | null | undefined>, default: false },
  progress: { type: Number },
  project: { type: Object as PropType<Project>, required: true },
  refundSum: { type: Object as PropType<Money> },
  showAdvanceOverflow: { type: Boolean, default: true }
})

const addUpTableData = computed(() => getAddUpTableData(formatter, props.addUp, props.progress !== undefined))
</script>
