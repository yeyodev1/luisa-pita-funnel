<script setup lang="ts">
/**
 * NoSpaceView.vue — /sin-cupo
 *
 * Página de rechazo empático cuando no califica en QualifyModal.
 * Cooldown 48h: el contador en pantalla muestra cuándo puede volver a aplicar.
 */
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

const COOLDOWN_MS = 48 * 60 * 60 * 1000

const now = ref(Date.now())
const disqAt = ref<number>(0)

const remainingMs = computed(() => {
  if (!disqAt.value) return 0
  return Math.max(0, disqAt.value + COOLDOWN_MS - now.value)
})

const canReapply = computed(() => remainingMs.value <= 0)

const countdown = computed(() => {
  const total = Math.floor(remainingMs.value / 1000)
  const h = String(Math.floor(total / 3600)).padStart(2, '0')
  const m = String(Math.floor((total % 3600) / 60)).padStart(2, '0')
  const s = String(total % 60).padStart(2, '0')
  return `${h}:${m}:${s}`
})

let timerId: ReturnType<typeof setInterval> | null = null

onMounted(() => {
  disqAt.value = Number(localStorage.getItem('lpb_disq_at') ?? '0')
  now.value = Date.now()
  timerId = setInterval(() => {
    now.value = Date.now()
  }, 1000)
})

onBeforeUnmount(() => {
  if (timerId) clearInterval(timerId)
})

const reapply = () => {
  localStorage.removeItem('lpb_disq_at')
  window.location.href = '/'
}
</script>

<template>
  <main class="ns">
    <section class="ns__container">
      <span class="ns__icon" aria-hidden="true">
        <i class="fa-solid fa-heart" />
      </span>

      <h1 class="ns__title">Esta cohorte no es para ti hoy — y está bien.</h1>

      <p class="ns__lead">
        La comunidad anual de Luisa Pita Bejarano está hecha para mujeres que pueden invertir
        <strong>capital tres cifras</strong> y comprometerse <strong>un año entero</strong>.
        Si hoy no es tu momento, no fuerces algo que no calza con tu vida real.
      </p>

      <div class="ns__cooldown" :class="{ 'ns__cooldown--ready': canReapply }">
        <p class="ns__cooldown-label">
          <template v-if="canReapply">
            <i class="fa-solid fa-circle-check" aria-hidden="true" />
            Ya puedes volver a aplicar
          </template>
          <template v-else>
            <i class="fa-solid fa-hourglass-half" aria-hidden="true" />
            Podrás volver a intentarlo en
          </template>
        </p>
        <p v-if="!canReapply" class="ns__cooldown-clock">
          {{ countdown }}
        </p>
        <p v-if="!canReapply" class="ns__cooldown-hint">
          48 horas desde que respondiste — si tu situación cambia, estaremos aquí.
        </p>

        <button v-if="canReapply" class="ns__cooldown-cta" type="button" @click="reapply">
          Volver a aplicar ahora
          <i class="fa-solid fa-arrow-right" aria-hidden="true" />
        </button>
      </div>

      <div class="ns__card">
        <h2>Mientras tanto, no te quedes lejos.</h2>
        <p>
          Sigue el trabajo de Luisa en Instagram. Ahí compartirá rutinas, reflexiones y novedades de la
          comunidad. Cuando sientas que es tu momento, vuelves y aplicas a la próxima cohorte.
        </p>
        <a
          href="https://www.instagram.com/luisapitabejarano/"
          target="_blank"
          rel="noopener noreferrer"
          class="ns__link"
        >
          <i class="fa-brands fa-instagram" aria-hidden="true" />
          Seguir a @luisapitabejarano
        </a>
      </div>

      <nav class="ns__legal">
        <RouterLink to="/politicas-privacidad">Privacidad</RouterLink>
        <RouterLink to="/aviso-legal">Aviso legal</RouterLink>
      </nav>
    </section>
  </main>
</template>

<style lang="scss" scoped>
.ns {
  min-height: 100vh;
  background: linear-gradient(180deg, #f5fffa 0%, #ffffff 100%);
  display: grid;
  place-items: center;
  padding: 3rem 1.5rem;
  font-family: 'Plus Jakarta Sans', system-ui, sans-serif;
  color: #0d1117;
}

.ns__container {
  max-width: 640px;
  width: 100%;
  text-align: center;
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: center;
}

.ns__icon {
  width: 4rem;
  height: 4rem;
  border-radius: 999px;
  display: grid;
  place-items: center;
  background: #16c784;
  color: #0d1117;
  font-size: 1.5rem;
  margin-bottom: 0.5rem;
}

.ns__title {
  font-family: 'Outfit', system-ui, sans-serif;
  font-weight: 800;
  font-size: clamp(1.6rem, 4vw, 2.3rem);
  margin: 0;
  line-height: 1.15;
}

.ns__lead {
  font-size: 1rem;
  color: #41484f;
  line-height: 1.6;
  margin: 0;

  strong {
    color: #0d1117;
    font-weight: 700;
  }
}

.ns__cooldown {
  margin-top: 0.5rem;
  background: #0d1117;
  color: #ffffff;
  border-radius: 1.25rem;
  padding: 1.5rem 1.5rem;
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  align-items: center;
  border: 2px solid rgba(22, 199, 132, 0.25);
}

.ns__cooldown--ready {
  background: linear-gradient(180deg, #16c784 0%, #0a9e68 100%);
  color: #0d1117;
  border-color: rgba(13, 17, 23, 0.15);
}

.ns__cooldown-label {
  margin: 0;
  font-family: 'Space Grotesk', system-ui, sans-serif;
  font-weight: 700;
  font-size: 0.85rem;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  opacity: 0.85;
}

.ns__cooldown-clock {
  margin: 0;
  font-family: 'Space Grotesk', system-ui, sans-serif;
  font-weight: 800;
  font-size: clamp(1.85rem, 6vw, 2.4rem);
  font-variant-numeric: tabular-nums;
  letter-spacing: 0.06em;
  color: #16c784;
}

.ns__cooldown-hint {
  margin: 0;
  font-size: 0.82rem;
  opacity: 0.7;
}

.ns__cooldown-cta {
  margin-top: 0.5rem;
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  background: #0d1117;
  color: #16c784;
  border: 0;
  padding: 0.85rem 1.5rem;
  border-radius: 999px;
  font-family: 'Space Grotesk', system-ui, sans-serif;
  font-weight: 700;
  cursor: pointer;
  transition: transform 140ms ease, background 160ms ease, color 160ms ease;

  &:hover {
    background: #ffffff;
    color: #0d1117;
    transform: translateY(-1px);
  }
}

.ns__card {
  margin-top: 1rem;
  background: #ffffff;
  border: 1.5px solid #e5e9ec;
  border-radius: 1.25rem;
  padding: 1.75rem 1.5rem;
  text-align: left;

  h2 {
    font-family: 'Outfit', system-ui, sans-serif;
    font-weight: 700;
    font-size: 1.15rem;
    margin: 0 0 0.5rem;
    text-align: center;
  }

  p {
    color: #4b5563;
    margin: 0 0 1.25rem;
    line-height: 1.55;
    text-align: center;
  }
}

.ns__link {
  display: inline-flex;
  align-items: center;
  gap: 0.55rem;
  background: #0d1117;
  color: #16c784;
  padding: 0.85rem 1.5rem;
  border-radius: 999px;
  text-decoration: none;
  font-family: 'Space Grotesk', system-ui, sans-serif;
  font-weight: 700;
  margin: 0 auto;
  width: max-content;
  transition: background 160ms ease, color 160ms ease;

  &:hover {
    background: #16c784;
    color: #0d1117;
  }
}

.ns__legal {
  display: flex;
  gap: 1.25rem;
  margin-top: 1rem;
  font-size: 0.85rem;

  a {
    color: #4b5563;
    text-decoration: none;

    &:hover {
      color: #0a9e68;
      text-decoration: underline;
    }
  }
}
</style>
