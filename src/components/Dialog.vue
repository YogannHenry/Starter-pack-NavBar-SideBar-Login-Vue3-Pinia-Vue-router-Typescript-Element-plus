<script lang="ts" setup>
import { CloseBold } from '@element-plus/icons-vue'
import isMobile from '@/composables/isMobile'
import { transitions } from '@/appConfig'

const props = withDefaults(defineProps<{
  show: boolean
  width?: string
  height?: string
  maxWidth?: string
  maxHeight?: string
  loading?: boolean
  preventShadowEvent?: boolean
  showClose?: boolean
  transition?: transitions
}>(), {
  width: '40rem',
  height: '50vh',
  maxWidth: '90vw',
  maxHeight: '90vh',
  loading: false,
  preventShadowEvent: true,
  showClose: true,
  transition: transitions.slideDown
})

const _isMobile = isMobile()

const emit = defineEmits<{
  (e: 'update:show', value: boolean): void
}>()

function shadowClick() {
  // If the close button is displayed (the close button will be automatically hidden on mobile devices, and clicking on the shadow will redirect to close the prompt).
  if (props.showClose) {
    // And if it's not on a mobile device, then delegate the closing authority to the close button.
    if (!_isMobile.value && props.preventShadowEvent) return
  }
  emit('update:show', false)
}

</script>
<template>
  <Teleport to="body">
    <Transition :name="props.transition" mode="out-in" appear>
      <Shadow v-if="props.show" contentCenter @shadowClick="shadowClick">
        <div :style="{ position: 'relative', maxHeight: _isMobile ? '80vh' : props.maxHeight }">
          <div class="block shadow modal"
            :style="{ padding: 0, width: props.loading ? '30rem' : props.width, height: props.loading ? '20rem' : props.height, maxWidth: props.maxWidth, maxHeight: _isMobile ? '80vh' : props.maxHeight }">
            <Loading v-if="props.loading"></Loading>
            <template v-else>
              <header class="modal-header">
                <slot name="modalHeader"></slot>
              </header>
              <main class="modal-main">
                <slot></slot>
              </main>
              <footer class="modal-footer">
                <slot name="modalFooter"></slot>
              </footer>
            </template>
          </div>
          <ElIcon v-if="!_isMobile && props.showClose" class="icon-close" @click="emit('update:show', false)">
            <CloseBold />
          </ElIcon>
        </div>
      </Shadow>
    </Transition>
  </Teleport>
</template>
<style scoped>
.modal {
  position: relative;
  display: flex;
  flex-direction: column;
  background-color: white;
  transition: ease .2s;
  transition-property: width, height;
}

.modal-header,
.modal-footer {
  padding: 0 1rem;
  min-height: 3rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.modal-header {
  font-size: 1.1rem;
  border-bottom: 5px solid var(--light-gray);
}

.modal-footer {
  border-top: 5px solid var(--light-gray);
}

.modal-main {
  flex: 1;
  overflow: auto;
  width: 100%;
  height: 100%;
  font-size: 0.9rem;
  padding: 1rem;
}

.icon-close {
  position: absolute;
  top: .3rem;
  right: -2rem;
  cursor: pointer;
  font-size: 1.5rem;
  color: white;
}
</style>