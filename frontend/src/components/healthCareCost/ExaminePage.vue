<template>
  <div>
    <ModalComponent
      ref="modalComp"
      :header="
        modalMode === 'add' ? $t('labels.newX', { X: $t('labels.healthCareCost') }) : $t('labels.editX', { X: $t('labels.healthCareCost') })
      ">
      <div v-if="modalHealthCareCost">
        <HealthCareCostForm
          :mode="modalMode"
          :healthCareCost="modalHealthCareCost"
          :loading="modalFormIsLoading"
          endpointPrefix="examine/"
          @cancel="resetAndHide()"
          @add="addHealthCareCost">
        </HealthCareCostForm>
      </div>
    </ModalComponent>
    <div class="container py-3">
      <div class="row mb-3 justify-content-end gx-4 gy-2">
        <div class="col-auto me-auto">
          <h2>{{ $t('accesses.examine/healthCareCost') }}</h2>
        </div>
        <div class="col-auto">
          <button class="btn btn-secondary" @click="showModal('add', undefined)">
            <i class="bi bi-plus-lg"></i>
            <span class="ms-1">{{ $t('labels.createX', { X: $t('labels.healthCareCost') }) }}</span>
          </button>
        </div>
      </div>
      <HealthCareCostList
        class="mb-5"
        endpoint="examine/healthCareCost"
        stateFilter="underExamination"
        :columns-to-hide="['state', 'editor', 'updatedAt', 'report', 'organisation', 'log.underExamination.date', 'bookingRemark']">
      </HealthCareCostList>
      <template v-if="!show">
        <button type="button" class="btn btn-light me-2" @click="show = 'underExaminationByInsurance'">
          {{ $t('labels.showX', { X: $t('labels.underExaminationByInsuranceHealthCareCosts') }) }} <i class="bi bi-chevron-down"></i>
        </button>
        <button type="button" class="btn btn-light" @click="show = 'inWork'">
          {{ $t('labels.showX', { X: $t('labels.inWorkX', { X: $t('labels.healthCareCosts') }) }) }} <i class="bi bi-chevron-down"></i>
        </button>
      </template>
      <template v-else>
        <button type="button" class="btn btn-light" @click="show = null">{{ $t('labels.hide') }} <i class="bi bi-chevron-up"></i></button>
        <hr class="hr" />
        <HealthCareCostList
          endpoint="examine/healthCareCost"
          :stateFilter="show"
          :columns-to-hide="['state', 'report', 'organisation', 'log.underExamination.date']">
        </HealthCareCostList>
      </template>
    </div>
  </div>
</template>

<script lang="ts">
import { HealthCareCostSimple } from '@/../../common/types.js'
import API from '@/api.js'
import APP_LOADER from '@/appData.js'
import ModalComponent from '@/components/elements/ModalComponent.vue'
import HealthCareCostList from '@/components/healthCareCost/HealthCareCostList.vue'
import HealthCareCostForm from '@/components/healthCareCost/forms/HealthCareCostForm.vue'
import { defineComponent } from 'vue'

type ModalMode = 'add' | 'edit'

export default defineComponent({
  name: 'ExaminePage',
  components: { HealthCareCostList, HealthCareCostForm, ModalComponent },
  props: [],
  data() {
    return {
      modalHealthCareCost: {} as Partial<HealthCareCostSimple>,
      modalMode: 'add' as ModalMode,
      show: null as 'inWork' | 'underExaminationByInsurance' | null,
      modalFormIsLoading: false
    }
  },
  methods: {
    showModal(mode: ModalMode, healthCareCost?: Partial<HealthCareCostSimple>) {
      if (healthCareCost) {
        this.modalHealthCareCost = healthCareCost
      }
      this.modalMode = mode
      if ((this.$refs.modalComp as typeof ModalComponent).modal) {
        ;(this.$refs.modalComp as typeof ModalComponent).modal.show()
      }
    },
    hideModal() {
      if ((this.$refs.modalComp as typeof ModalComponent).modal) {
        ;(this.$refs.modalComp as typeof ModalComponent).hideModal()
      }
    },
    resetModal() {
      this.modalMode = 'add'
      this.modalHealthCareCost = {}
    },
    resetAndHide() {
      this.resetModal()
      this.hideModal()
    },
    async addHealthCareCost(healthCareCost: HealthCareCostSimple) {
      this.modalFormIsLoading = true
      const result = await API.setter<HealthCareCostSimple>('examine/healthCareCost/inWork', healthCareCost)
      this.modalFormIsLoading = false
      if (result.ok) {
        this.resetAndHide()
      }
    }
  },
  async created() {
    await APP_LOADER.loadData()
  }
})
</script>

<style></style>
