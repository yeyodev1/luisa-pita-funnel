<script setup lang="ts">
import { ref, watch, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'
import { trackStage, generateEventId } from '@/utils/ghl'
import { useContactStore } from '@/stores/contact'

const props = defineProps<{ open: boolean }>()
const emit = defineEmits<{ (e: 'close'): void }>()

const router = useRouter()
const contactStore = useContactStore()
const IS_DEV = window.location.hostname === 'localhost'

const submitting = ref(false)
const touched = ref(false)

const form = ref({
  inversion: '',   // Q1
  gestion: '',     // Q2
})

const isValid = () => !!form.value.inversion && !!form.value.gestion

const qualifies = () => {
  if (form.value.inversion === 'c' || form.value.gestion === 'c') return false
  return true
}

const handleSubmit = async () => {
  touched.value = true
  if (!isValid()) return

  submitting.value = true
  const contact = contactStore.get()
  const califica = qualifies()
  const scheduleEventId = generateEventId('schedule')

  const inversionLabel: Record<string, string> = {
    a: 'A) Listo para invertir en un sistema profesional',
    b: 'B) Puede invertir algo, busca opción económica',
    c: 'C) Solo busca información gratuita',
  }
  const gestionLabel: Record<string, string> = {
    a: 'A) Outsourcing total: experto diseña, yo ejecuto',
    b: 'B) Quiere aprender para armar su propio plan',
    c: 'C) Prefiere probar rutinas aleatorias de internet',
  }

  const etiquetas = [
    'funnel-luisapita',
    'step-2-cualificacion',
    califica ? 'califica-lpb' : 'no-califica-lpb',
    `inversion-${form.value.inversion}`,
    `gestion-${form.value.gestion}`,
  ]

  const notas = `
━━━━━━━━━━━━━━━━━━━━━━━━
💪 LUISA PITA — Cualificación
━━━━━━━━━━━━━━━━━━━━━━━━
👤 ${contact.nombre} ${contact.apellido}
📧 ${contact.email}
📱 ${contact.telefono}
━━━━━━━━━━━━━━━━━━━━━━━━
💰 Inversión: ${inversionLabel[form.value.inversion] ?? form.value.inversion}
🎯 Gestión: ${gestionLabel[form.value.gestion] ?? form.value.gestion}
━━━━━━━━━━━━━━━━━━━━━━━━
${califica ? '✅ CALIFICA' : '❌ NO CALIFICA'}
  `.trim()

  const payload = {
    nombre: contact.nombre,
    apellido: contact.apellido,
    email: contact.email,
    telefono: contact.telefono,
    inversion: form.value.inversion,
    gestion: form.value.gestion,
    califica: califica ? 'si' : 'no',
    etiquetas: etiquetas.join(','),
    notas,
    nota: notas,
    event_id: scheduleEventId,
  }

  trackStage('cualificacion_completada', payload)

  await fetch(import.meta.env.VITE_WEBHOOK_CALIFICACION, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(payload),
  }).catch(() => {})

  ;(window as any).fbq?.('track', 'CompleteRegistration',
    { content_name: 'cualificacion-step2', status: califica ? 'califica' : 'no-califica' },
    { eventID: scheduleEventId }
  )

  submitting.value = false
  emit('close')

  if (califica) {
    ;(window as any).fbq?.('track', 'Lead')
    router.push('/agendar')
  } else {
    if (!IS_DEV) localStorage.setItem('lpb_disq_at', String(Date.now()))
    router.push('/sin-espacio')
  }
}

const onKeydown = (e: KeyboardEvent) => { if (e.key === 'Escape') emit('close') }

onMounted(() => document.addEventListener('keydown', onKeydown))
onUnmounted(() => document.removeEventListener('keydown', onKeydown))

watch(() => props.open, (v) => {
  if (v) {
    touched.value = false
    form.value = { inversion: '', gestion: '' }
  }
  document.body.style.overflow = v ? 'hidden' : ''
})
</script>

<template>
  <Teleport to="body">
    <Transition name="cal-fade">
      <div v-if="open" class="cal-backdrop" @click.self="emit('close')" role="dialog" aria-modal="true" aria-labelledby="cal-title">

        <div class="cal-modal">

          <button class="cal-close" @click="emit('close')" aria-label="Cerrar">
            <i class="fa-solid fa-xmark"></i>
          </button>

          <div class="cal-header">
            <div class="cal-header-icon" aria-hidden="true">
              <i class="fa-solid fa-heart-pulse"></i>
            </div>
            <h2 id="cal-title" class="cal-title">
              Solo dos preguntas
              <span class="cal-accent">antes de continuar</span>
            </h2>
            <p class="cal-subtitle">Menos de 30 segundos — para asegurarnos de que el programa es para ti.</p>
          </div>

          <form class="cal-form" @submit.prevent="handleSubmit" novalidate>

            <!-- Q1 — Inversión -->
            <fieldset class="cal-fieldset" :class="{ 'has-error': touched && !form.inversion }">
              <legend class="cal-legend">
                <span class="cal-q-num">01</span>
                Respecto a tu transformación física, ¿en qué punto te encuentras hoy?
              </legend>
              <div class="cal-options">
                <label v-for="opt in [
                  { value: 'a', label: 'Estoy lista para invertir en un sistema profesional que me ahorre tiempo y garantice resultados' },
                  { value: 'b', label: 'Puedo invertir algo de dinero, pero busco la opción más económica del mercado' },
                  { value: 'c', label: 'Solo busco información gratuita por ahora y no tengo intención de invertir' },
                ]" :key="opt.value" class="cal-option" :class="{ selected: form.inversion === opt.value }">
                  <input type="radio" :value="opt.value" v-model="form.inversion" hidden />
                  <span class="cal-option__radio" aria-hidden="true" />
                  <span class="cal-option__label">
                    <strong>{{ opt.value.toUpperCase() }})</strong> {{ opt.label }}
                  </span>
                </label>
              </div>
              <span v-if="touched && !form.inversion" class="cal-error">Selecciona una opción</span>
            </fieldset>

            <!-- Q2 — Gestión -->
            <fieldset class="cal-fieldset" :class="{ 'has-error': touched && !form.gestion }">
              <legend class="cal-legend">
                <span class="cal-q-num">02</span>
                ¿Cómo prefieres gestionar tu salud y entrenamiento en este momento?
              </legend>
              <div class="cal-options">
                <label v-for="opt in [
                  { value: 'a', label: 'Quiero delegar todo: que una experta diseñe mi plan y yo solo seguirlo para ver resultados' },
                  { value: 'b', label: 'Quiero aprender un poco de todo para intentar armar mi propio plan a largo plazo' },
                  { value: 'c', label: 'Prefiero seguir probando rutinas aleatorias que encuentro en internet' },
                ]" :key="opt.value" class="cal-option" :class="{ selected: form.gestion === opt.value }">
                  <input type="radio" :value="opt.value" v-model="form.gestion" hidden />
                  <span class="cal-option__radio" aria-hidden="true" />
                  <span class="cal-option__label">
                    <strong>{{ opt.value.toUpperCase() }})</strong> {{ opt.label }}
                  </span>
                </label>
              </div>
              <span v-if="touched && !form.gestion" class="cal-error">Selecciona una opción</span>
            </fieldset>

            <button type="submit" class="cal-submit" :disabled="submitting">
              <span v-if="!submitting">
                <i class="fa-solid fa-arrow-right" aria-hidden="true"></i>
                Ver mi resultado
              </span>
              <span v-else>
                <i class="fa-solid fa-spinner fa-spin" aria-hidden="true"></i>
                Procesando...
              </span>
            </button>

          </form>

        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<style lang="scss" scoped>
@use '@/styles/fonts.modules.scss' as fonts;
@use '@/styles/colorVariables.module.scss' as colors;

.cal-fade-enter-active,
.cal-fade-leave-active { transition: opacity 0.25s ease; }
.cal-fade-enter-from,
.cal-fade-leave-to { opacity: 0; }

.cal-backdrop {
  position: fixed;
  inset: 0;
  z-index: 900;
  background: rgba(colors.$OS-DARK, 0.65);
  backdrop-filter: blur(4px);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1rem;
  overflow-y: auto;
}

.cal-modal {
  background: #ffffff;
  border-radius: 20px;
  width: 100%;
  max-width: 560px;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  box-shadow: 0 24px 80px rgba(0, 0, 0, 0.2);
  border: 1px solid #D1FAE5;
}

.cal-close {
  position: absolute;
  top: 1rem;
  right: 1rem;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  border: none;
  background: #F0FFF8;
  color: #8A9BB5;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.9rem;
  transition: background 0.2s, color 0.2s;
  z-index: 1;
  &:hover { background: #D1FAE5; color: colors.$OS-DARK; }
}

.cal-header {
  padding: 2rem 2rem 1.25rem;
  border-bottom: 1px solid #F0FFF8;
  text-align: center;
}

.cal-header-icon {
  width: 52px;
  height: 52px;
  border-radius: 14px;
  background: colors.$OS-RED;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 1rem;
  i { color: #ffffff; font-size: 1.4rem; }
}

.cal-title {
  @include fonts.heading-font(800);
  font-size: 1.45rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.5rem;
  line-height: 1.25;
  letter-spacing: -0.02em;
}

.cal-accent { color: colors.$OS-RED; }

.cal-subtitle {
  font-size: 0.86rem;
  color: #8A9BB5;
  margin: 0;
}

.cal-form {
  padding: 1.5rem 2rem 2rem;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.cal-fieldset {
  border: none;
  padding: 0;
  margin: 0;

  &.has-error .cal-options { border-color: colors.$OS-RED; border-radius: 10px; }
}

.cal-legend {
  display: flex;
  align-items: flex-start;
  gap: 0.5rem;
  font-family: fonts.$font-interface;
  font-size: 0.88rem;
  font-weight: 700;
  color: colors.$OS-DARK;
  margin-bottom: 0.75rem;
  line-height: 1.4;
}

.cal-q-num {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  min-width: 24px;
  border-radius: 6px;
  background: colors.$OS-RED;
  color: #ffffff;
  font-size: 0.72rem;
  font-weight: 800;
  flex-shrink: 0;
  margin-top: 1px;
}

.cal-options {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.cal-option {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  padding: 0.75rem 1rem;
  border: 1.5px solid #D1FAE5;
  border-radius: 10px;
  cursor: pointer;
  transition: border-color 0.18s, background 0.18s;
  background: #FFFBFD;

  &:hover { border-color: colors.$OS-RED; background: #F0FFF8; }

  &.selected {
    border-color: colors.$OS-RED;
    background: #F0FFF8;
  }

  &__radio {
    width: 18px;
    height: 18px;
    min-width: 18px;
    border-radius: 50%;
    border: 2px solid #E0C4D4;
    flex-shrink: 0;
    position: relative;
    transition: border-color 0.18s;
    margin-top: 2px;

    .cal-option.selected & {
      border-color: colors.$OS-RED;
      &::after {
        content: '';
        position: absolute;
        inset: 3px;
        border-radius: 50%;
        background: colors.$OS-RED;
      }
    }
  }

  &__label {
    font-size: 0.88rem;
    color: #3A4F6A;
    font-weight: 500;
    line-height: 1.45;

    strong { color: colors.$OS-DARK; font-weight: 700; }
    .cal-option.selected & { color: colors.$OS-DARK; }
  }
}

.cal-error {
  display: block;
  font-size: 0.78rem;
  color: colors.$OS-RED;
  margin-top: 0.35rem;
}

.cal-submit {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.6rem;
  background: colors.$OS-RED;
  color: #ffffff;
  border: none;
  border-radius: 12px;
  padding: 1rem 2rem;
  font-family: fonts.$font-accent;
  font-size: 0.97rem;
  font-weight: 800;
  letter-spacing: 0.04em;
  cursor: pointer;
  width: 100%;
  transition: background 0.2s ease, transform 0.15s ease;
  box-shadow: 0 4px 16px rgba(22, 199, 132, 0.3);

  &:hover:not(:disabled) {
    background: darken(#16C784, 8%);
    transform: translateY(-1px);
  }
  &:disabled { opacity: 0.65; cursor: not-allowed; }
}
</style>
