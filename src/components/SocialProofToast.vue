<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const LEADS = [
  { name: 'Ma••••••• R••••', city: 'Quito' },
  { name: 'Pa••••• M•••••', city: 'Guayaquil' },
  { name: 'Ca••••••• V•••', city: 'Cuenca' },
  { name: 'An••••• L•••••', city: 'Quito' },
  { name: 'Va••••••• G•••', city: 'Quito' },
  { name: 'Da••••• R•••••', city: 'Guayaquil' },
  { name: 'So••••• H•••••', city: 'Ambato' },
  { name: 'Na••••••• C•••', city: 'Loja' },
  { name: 'Lu••••• M•••••', city: 'Quito' },
  { name: 'Ga••••••• P•••', city: 'Manta' },
  { name: 'Is•••••• T•••••', city: 'Guayaquil' },
  { name: 'Ve••••••• A•••', city: 'Ibarra' },
]

const TIMES = ['Hace un momento', 'Hace 2 minutos', 'Hace 5 minutos', 'Hace 3 minutos', 'Hace 1 minuto']

const visible = ref(false)
const current = ref(LEADS[0])
const timeAgo = ref(TIMES[0])

let showTimer: ReturnType<typeof setTimeout>
let hideTimer: ReturnType<typeof setTimeout>
let idx = 0

function showNext() {
  idx = (idx + 1) % LEADS.length
  current.value = LEADS[idx]
  timeAgo.value = TIMES[Math.floor(Math.random() * TIMES.length)]
  visible.value = true

  hideTimer = setTimeout(() => {
    visible.value = false
    showTimer = setTimeout(showNext, 2000)
  }, 5000)
}

onMounted(() => {
  showTimer = setTimeout(showNext, 3000)
})

onUnmounted(() => {
  clearTimeout(showTimer)
  clearTimeout(hideTimer)
})
</script>

<template>
  <Transition name="toast">
    <div v-if="visible" class="sp-toast" role="status" aria-live="polite">
      <div class="sp-toast__avatar" aria-hidden="true">
        <i class="fa-solid fa-user"></i>
      </div>
      <div class="sp-toast__body">
        <p class="sp-toast__action">
          <i class="fa-solid fa-calendar-check" aria-hidden="true"></i>
          Acaba de agendar su sesión
        </p>
        <p class="sp-toast__name">{{ current.name }} · {{ current.city }} aseguró su lugar</p>
        <p class="sp-toast__time">{{ timeAgo }}</p>
      </div>
      <button class="sp-toast__close" @click="visible = false" aria-label="Cerrar">
        <i class="fa-solid fa-xmark"></i>
      </button>
    </div>
  </Transition>
</template>

<style lang="scss" scoped>
@use '@/styles/fonts.modules.scss' as fonts;
@use '@/styles/colorVariables.module.scss' as colors;

.sp-toast {
  position: fixed;
  bottom: 1.5rem;
  left: 1.25rem;
  z-index: 9999;
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  background: #ffffff;
  border: 1px solid #D1FAE5;
  border-left: 4px solid colors.$LPB-PINK;
  border-radius: 14px;
  padding: 0.85rem 1rem 0.85rem 0.9rem;
  max-width: 300px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.12), 0 2px 8px rgba(22, 199, 132, 0.1);
  cursor: default;

  @media (max-width: 420px) {
    max-width: calc(100vw - 2rem);
    bottom: 1rem;
    left: 1rem;
  }
}

.sp-toast__avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: linear-gradient(135deg, colors.$LPB-PINK, colors.$LPB-PURPLE);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  i { color: #ffffff; font-size: 0.85rem; }
}

.sp-toast__body {
  flex: 1;
  min-width: 0;
}

.sp-toast__action {
  font-family: fonts.$font-interface;
  font-size: 0.72rem;
  font-weight: 700;
  color: colors.$LPB-PINK;
  text-transform: uppercase;
  letter-spacing: 0.04em;
  margin: 0 0 0.2rem;
  display: flex;
  align-items: center;
  gap: 0.35rem;
  i { font-size: 0.68rem; }
}

.sp-toast__name {
  font-size: 0.82rem;
  font-weight: 600;
  color: colors.$LPB-DARK;
  margin: 0 0 0.15rem;
  line-height: 1.35;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.sp-toast__time {
  font-size: 0.72rem;
  color: #A0B0C5;
  margin: 0;
}

.sp-toast__close {
  background: none;
  border: none;
  padding: 0;
  cursor: pointer;
  color: #C0D0E0;
  font-size: 0.8rem;
  flex-shrink: 0;
  margin-top: 1px;
  transition: color 0.15s;
  &:hover { color: colors.$LPB-DARK; }
}

// ── Transition ────────────────────────────────────────────────────────────────
.toast-enter-active { animation: toast-in 0.4s cubic-bezier(0.34, 1.56, 0.64, 1); }
.toast-leave-active { animation: toast-out 0.3s ease forwards; }

@keyframes toast-in {
  from { opacity: 0; transform: translateY(20px) scale(0.95); }
  to   { opacity: 1; transform: translateY(0) scale(1); }
}

@keyframes toast-out {
  from { opacity: 1; transform: translateY(0) scale(1); }
  to   { opacity: 0; transform: translateY(10px) scale(0.97); }
}
</style>
