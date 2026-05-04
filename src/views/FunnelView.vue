<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'
import RegistrationModal from '@/components/RegistrationModal.vue'
import { captureFbParams } from '@/utils/fbclid'
const CDN = 'https://res.cloudinary.com/dkosgkjpq/image/upload'
const LUISA_AUTHORITY = `${CDN}/w_560,h_700,c_fill,g_face,q_auto,f_auto/luisa-pita/luisa-11.jpg`
const LUISA_VSL = `${CDN}/w_1280,h_720,c_fill,g_face,q_auto,f_auto/luisa-pita/luisa-2.jpg`

const router = useRouter()
const modalOpen = ref(false)
const IS_DEV = window.location.hostname === 'localhost'

const openModal = () => {
  if (!IS_DEV) {
    const disqAt = localStorage.getItem('lpb_disq_at')
    if (disqAt && Date.now() - Number(disqAt) < 24 * 60 * 60 * 1000) {
      router.push('/sin-espacio')
      return
    }
  }
  modalOpen.value = true
}

const stats = [
  {
    icon: 'fa-solid fa-users',
    number: '200+',
    text: 'Mujeres transformadas con el programa',
  },
  {
    icon: 'fa-solid fa-calendar-check',
    number: '8',
    text: 'Semanas para resultados reales y duraderos',
  },
  {
    icon: 'fa-solid fa-heart-pulse',
    number: '97%',
    text: 'Tasa de éxito sin efecto rebote',
  },
]

const pillars = [
  'Sin dietas restrictivas que te hacen sufrir y abandonar',
  'Sin rutinas de 2 horas imposibles para tu agenda',
  'Sin el efecto rebote de siempre',
  'Con un plan personalizado que funciona para mujeres ocupadas',
]

const methodology = [
  {
    num: '01',
    icon: 'fa-solid fa-clipboard-list',
    title: 'Evaluación Personalizada',
    body: 'Analizamos tu historial, metabolismo y rutina diaria para crear un plan 100% adaptado a ti y tu estilo de vida.',
  },
  {
    num: '02',
    icon: 'fa-solid fa-bowl-food',
    title: 'Plan Nutricional Flexible',
    body: 'Nada de prohibiciones. Aprendes a comer de forma inteligente, sin pasar hambre, encajando en tu vida real.',
  },
  {
    num: '03',
    icon: 'fa-solid fa-fire-flame-curved',
    title: 'Movimiento Eficiente',
    body: 'Rutinas de 20-30 minutos diseñadas para mujeres ocupadas. Máximos resultados con el mínimo tiempo disponible.',
  },
]

// Countdown urgency (24h rolling)
const hours = ref('23')
const minutes = ref('47')
const seconds = ref('12')
let interval: ReturnType<typeof setInterval>

onMounted(() => {
  captureFbParams()
  let total = 23 * 3600 + 47 * 60 + 12
  interval = setInterval(() => {
    total--
    if (total <= 0) total = 23 * 3600 + 59 * 60 + 59
    hours.value = String(Math.floor(total / 3600)).padStart(2, '0')
    minutes.value = String(Math.floor((total % 3600) / 60)).padStart(2, '0')
    seconds.value = String(total % 60).padStart(2, '0')
  }, 1000)
})

onUnmounted(() => clearInterval(interval))
</script>

<template>
  <div class="funnel">

    <!-- TOP BAR -->
    <header class="funnel__topbar">
      <h2 class="funnel__logo-text">LUISA PITA BEJARANO</h2>
    </header>

    <!-- URGENCY BANNER -->
    <div class="funnel__urgency" role="banner">
      <span class="funnel__urgency-dot" aria-hidden="true" />
      <span>Cupos limitados — expiran en:</span>
      <div class="funnel__timer" aria-live="polite" aria-label="Tiempo restante">
        <span class="funnel__timer-block"><strong>{{ hours }}</strong><small>h</small></span>
        <span class="funnel__timer-sep" aria-hidden="true">:</span>
        <span class="funnel__timer-block"><strong>{{ minutes }}</strong><small>m</small></span>
        <span class="funnel__timer-sep" aria-hidden="true">:</span>
        <span class="funnel__timer-block"><strong>{{ seconds }}</strong><small>s</small></span>
      </div>
    </div>

    <!-- HERO -->
    <section class="funnel__hero" aria-labelledby="funnel-headline">
      <div class="funnel__container">


        <p class="funnel__eyebrow">
          <i class="fa-solid fa-heart-pulse" aria-hidden="true"></i>
          Programa de Transformación para Mujeres Ocupadas
        </p>

        <h1 id="funnel-headline" class="funnel__headline">
          Baja 8 kilos en 8 semanas
          <span class="funnel__headline-accent">sin efecto rebote</span>
          — aunque ya lo hayas intentado antes
        </h1>

        <ul class="funnel__pillars" role="list">
          <li v-for="p in pillars" :key="p" class="funnel__pillar">
            <i class="fa-solid fa-check" aria-hidden="true"></i>
            {{ p }}
          </li>
        </ul>

        <!-- VSL Gated Area -->
        <div class="funnel__vsl-wrap">
          <div class="funnel__vsl" @click="openModal()" role="button" aria-label="Ver video" tabindex="0">
            <div class="funnel__vsl-bg">
              <img :src="LUISA_VSL" class="funnel__vsl-thumb" alt="Luisa Pita Bejarano — Ver el video" />
              <div class="funnel__vsl-vignette"></div>
            </div>
            <div class="funnel__vsl-overlay">
              <div class="funnel__vsl-play-wrap">
                <div class="funnel__vsl-play">
                  <i class="fa-solid fa-play" aria-hidden="true"></i>
                </div>
                <span class="funnel__vsl-play-label">Ver el video gratis</span>
              </div>
              <p class="funnel__vsl-caption">Descubre el método exacto que usaron más de 200 mujeres para bajar 8 kilos en 8 semanas — sin efecto rebote</p>
            </div>
            <div class="funnel__vsl-duration" aria-hidden="true">
              <i class="fa-solid fa-circle-play"></i>
              Video de 8 min
            </div>
          </div>
        </div>

        <!-- CTA -->
        <div class="funnel__cta-wrap">
          <button class="funnel__cta-btn" @click="openModal()">
            <i class="fa-solid fa-arrow-right" aria-hidden="true"></i>
            QUIERO MI PLAN PERSONALIZADO
          </button>
          <p class="funnel__cta-sub">
            <i class="fa-solid fa-lock" aria-hidden="true"></i>
            Cupos limitados &nbsp;·&nbsp; Sin compromiso &nbsp;·&nbsp; Acceso inmediato
          </p>
        </div>

      </div>
    </section>

    <!-- STATS -->
    <section class="funnel__stats" aria-label="Resultados comprobados">
      <div class="funnel__container">
        <p class="funnel__section-label funnel__section-label--light">Resultados reales — mujeres reales</p>
        <div class="funnel__stats-grid">
          <div v-for="stat in stats" :key="stat.number" class="funnel__stat">
            <div class="funnel__stat-icon" aria-hidden="true">
              <i :class="stat.icon"></i>
            </div>
            <strong class="funnel__stat-number">{{ stat.number }}</strong>
            <p class="funnel__stat-text">{{ stat.text }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- PROBLEMA -->
    <section class="funnel__problem" aria-labelledby="problem-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">¿Te identificas con esto?</p>
        <h2 id="problem-heading" class="funnel__section-title">
          La trampa en la que caen la mayoría de mujeres
        </h2>
        <div class="funnel__problem-grid">
          <div class="funnel__problem-item">
            <i class="fa-solid fa-triangle-exclamation funnel__problem-icon" aria-hidden="true"></i>
            <div>
              <strong>Dietas que duran 2 semanas</strong>
              <p>Restricción extrema → hambre → ansiedad → abandono → culpa. El ciclo de siempre que nunca lleva a resultados duraderos.</p>
            </div>
          </div>
          <div class="funnel__problem-item">
            <i class="fa-solid fa-triangle-exclamation funnel__problem-icon" aria-hidden="true"></i>
            <div>
              <strong>Rutinas de gym imposibles</strong>
              <p>Planes diseñados para personas con 2 horas libres al día. Si tienes trabajo, familia y responsabilidades, simplemente no encajan.</p>
            </div>
          </div>
          <div class="funnel__problem-item">
            <i class="fa-solid fa-triangle-exclamation funnel__problem-icon" aria-hidden="true"></i>
            <div>
              <strong>El efecto rebote inevitable</strong>
              <p>Bajas kilos rápido y los recuperas todos en pocas semanas. No es falta de voluntad — es que el método estaba mal desde el principio.</p>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- METODOLOGÍA -->
    <section class="funnel__method" aria-labelledby="method-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">Cómo funciona el programa</p>
        <h2 id="method-heading" class="funnel__section-title">
          Tres pilares que garantizan resultados sin rebote
        </h2>
        <div class="funnel__method-grid">
          <div v-for="m in methodology" :key="m.num" class="funnel__method-card">
            <div class="funnel__method-num" aria-hidden="true">{{ m.num }}</div>
            <div class="funnel__method-icon" aria-hidden="true">
              <i :class="m.icon"></i>
            </div>
            <h3 class="funnel__method-title">{{ m.title }}</h3>
            <p class="funnel__method-body">{{ m.body }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- TESTIMONIAL -->
    <section class="funnel__testimonial" aria-labelledby="testimonial-heading">
      <div class="funnel__container">
        <p class="funnel__section-label">Lo que dicen nuestras alumnas</p>
        <div class="funnel__testimonial-card">
          <i class="fa-solid fa-quote-left funnel__testimonial-quote" aria-hidden="true"></i>
          <blockquote class="funnel__testimonial-text">
            "Llevaba años intentando bajar de peso con dietas de moda y siempre recuperaba todo en dos meses.
            Con el programa de Luisa bajé 9 kilos en 8 semanas y lo más increíble es que ya van 6 meses y los mantuve.
            Por fin entendí cómo funciona mi cuerpo."
          </blockquote>
          <footer class="funnel__testimonial-author">
            <div class="funnel__testimonial-avatar" aria-hidden="true">
              <i class="fa-solid fa-user"></i>
            </div>
            <div>
              <strong>Madre de familia, ejecutiva</strong>
              <span>Participante del programa — 9 kg menos, sin rebote</span>
            </div>
          </footer>
        </div>
      </div>
    </section>

    <!-- AUTHORITY — Luisa Pita Bejarano -->
    <section class="funnel__authority" aria-labelledby="authority-heading">
      <div class="funnel__container funnel__authority-inner">
        <div class="funnel__authority-photo-wrap">
          <div class="funnel__authority-photo-frame">
            <img :src="LUISA_AUTHORITY" alt="Luisa Pita Bejarano" class="funnel__authority-img" loading="lazy" />
          </div>
        </div>
        <div class="funnel__authority-content">
          <p class="funnel__authority-eyebrow">Tu coach de transformación</p>
          <h2 id="authority-heading" class="funnel__authority-name">Luisa Pita Bejarano</h2>
          <p class="funnel__authority-role">Coach de Transformación Corporal para Mujeres Ocupadas</p>
          <p class="funnel__authority-bio">
            Especializada en ayudar a mujeres adultas ocupadas a perder grasa y tonificar su cuerpo
            sin dietas imposibles ni rutinas que no encajan en su vida real.
            Mi método elimina el efecto rebote porque ataca la causa raíz, no los síntomas.
          </p>
          <ul class="funnel__authority-creds" role="list">
            <li><i class="fa-solid fa-check-circle" aria-hidden="true"></i> Especialista en nutrición y hábitos sostenibles</li>
            <li><i class="fa-solid fa-check-circle" aria-hidden="true"></i> Método comprobado: 8 kilos en 8 semanas sin rebote</li>
            <li><i class="fa-solid fa-check-circle" aria-hidden="true"></i> Más de 200 mujeres transformadas con resultados duraderos</li>
          </ul>
        </div>
      </div>
    </section>

    <!-- CTA FINAL -->
    <section class="funnel__cta-final" aria-labelledby="cta-final-heading">
      <div class="funnel__container">
        <h2 id="cta-final-heading" class="funnel__cta-final-title">
          ¿Lista para tu transformación?
        </h2>
        <p class="funnel__cta-final-sub">
          Accede al video y descubre el método exacto que usaron más de 200 mujeres para bajar 8 kilos
          en 8 semanas — sin pasar hambre, sin rutinas imposibles y sin efecto rebote.
        </p>
        <button class="funnel__cta-btn" @click="openModal()">
          <i class="fa-solid fa-arrow-right" aria-hidden="true"></i>
          QUIERO MI PLAN PERSONALIZADO
        </button>
        <p class="funnel__cta-sub">
          <i class="fa-solid fa-lock" aria-hidden="true"></i>
          Cupos limitados &nbsp;·&nbsp; Acceso inmediato &nbsp;·&nbsp; Sin compromiso
        </p>
      </div>
    </section>

    <!-- FOOTER -->
    <footer class="funnel__footer">
      <div class="funnel__container funnel__footer-inner">
        <h2 class="funnel__footer-logo-text">LUISA PITA BEJARANO</h2>
        <nav class="funnel__footer-links" aria-label="Legal">
          <RouterLink to="/politicas-privacidad">Política de Privacidad</RouterLink>
          <RouterLink to="/aviso-legal">Aviso Legal</RouterLink>
        </nav>
        <p class="funnel__footer-copy">
          © {{ new Date().getFullYear() }} LUISA PITA BEJARANO. Todos los derechos reservados.
        </p>
      </div>
    </footer>

  </div>

  <RegistrationModal :open="modalOpen" @close="modalOpen = false" />
</template>

<style lang="scss" scoped>
@use '@/styles/fonts.modules.scss' as fonts;
@use '@/styles/colorVariables.module.scss' as colors;

.funnel {
  background: #ffffff;
  color: colors.$OS-DARK;
  min-height: 100vh;
  font-family: fonts.$font-secondary;

  &__container {
    max-width: 900px;
    margin: 0 auto;
    padding: 0 1.5rem;
    @media (min-width: 768px) { padding: 0 2rem; }
  }
}

// ── Top bar ──────────────────────────────────────────────────────────────────
.funnel__topbar {
  background: #ffffff;
  border-bottom: 1px solid #D1FAE5;
  padding: 0.9rem 1.5rem;
  display: flex;
  justify-content: center;
  position: sticky;
  top: 0;
  z-index: 100;
  box-shadow: 0 1px 12px rgba(22, 199, 132, 0.06);
}

.funnel__logo {
  height: 38px;
  width: auto;
  object-fit: contain;
}

// ── Urgency banner ───────────────────────────────────────────────────────────
.funnel__urgency {
  background: colors.$OS-NAVY;
  color: #ffffff;
  padding: 0.55rem 1.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.6rem;
  flex-wrap: wrap;
  font-family: fonts.$font-interface;
  font-size: 0.82rem;
  font-weight: 600;
  letter-spacing: 0.02em;
}

.funnel__urgency-dot {
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #4ADE80;
  flex-shrink: 0;
  animation: dot-pulse 1.5s infinite;
}

@keyframes dot-pulse {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(0.75); }
}

.funnel__timer {
  display: flex;
  align-items: center;
  gap: 0.15rem;
}

.funnel__timer-block {
  display: flex;
  align-items: baseline;
  gap: 1px;
  strong { font-size: 0.95rem; font-weight: 800; }
  small { font-size: 0.68rem; opacity: 0.75; }
}

.funnel__timer-sep { font-weight: 800; opacity: 0.5; padding: 0 1px; }

// ── Hero ─────────────────────────────────────────────────────────────────────
.funnel__hero {
  padding: 3.5rem 0 3rem;
  background: linear-gradient(180deg, #F0FFF8 0%, #ffffff 70%);
  text-align: center;

  .funnel__eyebrow { margin-left: auto; margin-right: auto; }
  .funnel__pillars { justify-content: center; }
  .funnel__cta-wrap { align-items: center; }
}


.funnel__eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  background: rgba(colors.$OS-RED, 0.06);
  border: 1px solid rgba(colors.$OS-RED, 0.14);
  border-radius: 999px;
  padding: 0.35rem 0.85rem;
  color: colors.$OS-RED;
  font-family: fonts.$font-interface;
  font-size: 0.78rem;
  font-weight: 700;
  letter-spacing: 0.05em;
  text-transform: uppercase;
  margin: 0 0 1.4rem;
  i { font-size: 0.8rem; }
}

.funnel__headline {
  @include fonts.heading-font(800);
  font-size: clamp(2rem, 5vw, 3.1rem);
  line-height: 1.15;
  color: colors.$OS-DARK;
  margin: 0 0 1.5rem;
  letter-spacing: -0.025em;

  &-accent { color: colors.$OS-RED; }
}

.funnel__vsl-player-container {
  width: 100%;
  border-radius: 24px;
  overflow: hidden;
  border: 1px solid rgba(colors.$OS-RED, 0.2);
  box-shadow: 0 40px 100px -20px rgba(0,0,0,0.4);
  background: #000;
  line-height: 0;

  @media (max-width: 768px) {
    border-radius: 12px;
  }
}

.funnel__pillars {
  list-style: none;
  padding: 0;
  margin: 0 0 2rem;
  display: flex;
  flex-direction: column;
  gap: 0.55rem;
}

.funnel__pillar {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  font-size: 0.93rem;
  color: #3A4F6A;

  i {
    color: colors.$OS-BLUE;
    font-size: 0.82rem;
    flex-shrink: 0;
  }
}

// ── VSL ──────────────────────────────────────────────────────────────────────
.funnel__vsl-wrap {
  margin: 2rem auto;
  max-width: 760px;
  width: 100%;
}

.funnel__vsl {
  position: relative;
  width: 100%;
  aspect-ratio: 16 / 9;
  border-radius: 16px;
  overflow: hidden;
  cursor: pointer;
  border: 2px solid rgba(colors.$OS-RED, 0.2);
  box-shadow: 0 8px 40px rgba(22, 199, 132, 0.12);
  transition: transform 0.2s ease, box-shadow 0.2s ease;

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 16px 48px rgba(22, 199, 132, 0.2);
  }
  &:focus-visible {
    outline: 3px solid colors.$OS-RED;
    outline-offset: 2px;
  }
}

.funnel__vsl-bg {
  position: absolute;
  inset: 0;
  background: #0D1117;
}

.funnel__vsl-thumb {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center top;
  filter: brightness(0.82);
  transform: scale(1.02);
  transition: transform 0.5s ease, filter 0.4s ease;

  .funnel__vsl:hover & {
    transform: scale(1.05);
    filter: brightness(0.9);
  }
}

.funnel__vsl-vignette {
  position: absolute;
  inset: 0;
  background:
    radial-gradient(ellipse at center, transparent 30%, rgba(0,0,0,0.55) 100%),
    linear-gradient(to bottom, rgba(0,0,0,0.15) 0%, transparent 30%, transparent 60%, rgba(0,0,0,0.65) 100%);
  z-index: 1;
}

.funnel__vsl-overlay {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  z-index: 2;
}

.funnel__vsl-play-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.6rem;
  transition: transform 0.2s ease;
  .funnel__vsl:hover & { transform: scale(1.06); }
}

.funnel__vsl-play {
  width: 88px;
  height: 88px;
  border-radius: 50%;
  background: colors.$OS-RED;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 0 0 8px rgba(22, 199, 132, 0.25), 0 8px 32px rgba(0, 0, 0, 0.4);
  transition: box-shadow 0.3s ease;

  .funnel__vsl:hover & {
    box-shadow: 0 0 0 14px rgba(22, 199, 132, 0.2), 0 12px 40px rgba(0, 0, 0, 0.5);
  }

  i {
    color: #ffffff;
    font-size: 2rem;
    margin-left: 6px;
  }
}

.funnel__vsl-play-label {
  color: #ffffff;
  font-family: fonts.$font-interface;
  font-size: 0.82rem;
  font-weight: 700;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  text-shadow: 0 1px 6px rgba(0,0,0,0.6);
  background: rgba(22, 199, 132, 0.85);
  padding: 0.3rem 0.85rem;
  border-radius: 999px;
}

.funnel__vsl-caption {
  color: rgba(#ffffff, 0.92);
  font-size: 0.86rem;
  font-weight: 600;
  text-align: center;
  padding: 0 1.5rem;
  text-shadow: 0 1px 8px rgba(0, 0, 0, 0.7);
  max-width: 380px;
  line-height: 1.45;
}

.funnel__vsl-duration {
  position: absolute;
  top: 0.85rem;
  right: 0.85rem;
  z-index: 3;
  display: flex;
  align-items: center;
  gap: 0.35rem;
  background: rgba(0,0,0,0.55);
  backdrop-filter: blur(4px);
  color: rgba(#ffffff, 0.9);
  font-family: fonts.$font-interface;
  font-size: 0.72rem;
  font-weight: 600;
  padding: 0.28rem 0.65rem;
  border-radius: 999px;
  i { font-size: 0.7rem; color: colors.$OS-RED; }
}

// ── CTA ──────────────────────────────────────────────────────────────────────
.funnel__cta-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.75rem;
}

.funnel__cta-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.6rem;
  background: colors.$OS-RED;
  color: #ffffff;
  border: none;
  border-radius: 12px;
  padding: 1.1rem 2.5rem;
  font-family: fonts.$font-accent;
  font-size: 1rem;
  font-weight: 800;
  letter-spacing: 0.05em;
  cursor: pointer;
  width: 100%;
  max-width: 480px;
  transition: background 0.2s ease, transform 0.15s ease, box-shadow 0.2s ease;
  box-shadow: 0 4px 20px rgba(22, 199, 132, 0.35);

  &:hover {
    background: darken(#16C784, 8%);
    transform: translateY(-1px);
    box-shadow: 0 8px 28px rgba(22, 199, 132, 0.45);
  }
  &:active { transform: translateY(0); }
}

.funnel__cta-sub {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.4rem;
  font-size: 0.78rem;
  color: #8A9BB5;
  margin: 0;
  i { font-size: 0.72rem; }
}

// ── Stats ────────────────────────────────────────────────────────────────────
.funnel__stats {
  background: colors.$OS-NAVY;
  padding: 3rem 0;
}

.funnel__section-label {
  font-family: fonts.$font-interface;
  font-size: 0.78rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: colors.$OS-NAVY;
  margin: 0 0 1rem;

  &--light { color: rgba(#ffffff, 0.55); }
}

.funnel__stats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 2rem;
  @media (max-width: 580px) {
    grid-template-columns: 1fr;
    gap: 1.75rem;
  }
}

.funnel__stat { text-align: center; }

.funnel__stat-icon {
  font-size: 1.6rem;
  color: rgba(#ffffff, 0.35);
  margin-bottom: 0.5rem;
}

.funnel__stat-number {
  display: block;
  @include fonts.heading-font(800);
  font-size: 2.6rem;
  color: #ffffff;
  line-height: 1;
  margin-bottom: 0.4rem;
  letter-spacing: -0.03em;
}

.funnel__stat-text {
  font-size: 0.83rem;
  color: rgba(#ffffff, 0.7);
  line-height: 1.45;
  margin: 0;
}

// ── Problem ──────────────────────────────────────────────────────────────────
.funnel__problem {
  padding: 4rem 0;
  background: #ffffff;
}

.funnel__section-title {
  @include fonts.heading-font(800);
  font-size: clamp(1.6rem, 3.5vw, 2.2rem);
  color: colors.$OS-DARK;
  margin: 0.25rem 0 2rem;
  line-height: 1.2;
  letter-spacing: -0.02em;
}

.funnel__problem-grid {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.funnel__problem-item {
  display: flex;
  gap: 1rem;
  align-items: flex-start;
  padding: 1.25rem;
  background: #F0FFF8;
  border: 1px solid #D1FAE5;
  border-radius: 12px;

  strong {
    display: block;
    color: colors.$OS-DARK;
    font-size: 0.93rem;
    font-weight: 700;
    margin-bottom: 0.2rem;
  }
  p {
    font-size: 0.86rem;
    color: #4A5F7A;
    line-height: 1.5;
    margin: 0;
  }
}

.funnel__problem-icon {
  font-size: 1.2rem;
  color: colors.$OS-RED;
  flex-shrink: 0;
  margin-top: 2px;
}

// ── Methodology ──────────────────────────────────────────────────────────────
.funnel__method {
  padding: 4rem 0;
  background: colors.$LPB-LIGHT;
}

.funnel__method-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.25rem;
  @media (max-width: 700px) { grid-template-columns: 1fr; }
}

.funnel__method-card {
  background: #ffffff;
  border: 1px solid #D1FAE5;
  border-radius: 16px;
  padding: 1.75rem 1.5rem;
  position: relative;
  box-shadow: 0 2px 12px rgba(22, 199, 132, 0.05);
}

.funnel__method-num {
  position: absolute;
  top: 1rem;
  right: 1.25rem;
  @include fonts.heading-font(800);
  font-size: 2.5rem;
  color: rgba(colors.$OS-NAVY, 0.07);
  line-height: 1;
  user-select: none;
}

.funnel__method-icon {
  width: 44px;
  height: 44px;
  border-radius: 10px;
  background: colors.$OS-RED;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1rem;
  i { color: #ffffff; font-size: 1.1rem; }
}

.funnel__method-title {
  @include fonts.heading-font(700);
  font-size: 0.97rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.5rem;
}

.funnel__method-body {
  font-size: 0.86rem;
  color: #4A5F7A;
  line-height: 1.55;
  margin: 0;
}

// ── Testimonial ──────────────────────────────────────────────────────────────
.funnel__testimonial {
  padding: 4rem 0;
  background: #ffffff;
}

.funnel__testimonial-card {
  background: #F0FFF8;
  border: 1px solid rgba(colors.$OS-RED, 0.1);
  border-left: 4px solid colors.$OS-RED;
  border-radius: 16px;
  padding: 2rem;
  max-width: 720px;
  margin: 0 auto;
  box-shadow: 0 4px 24px rgba(22, 199, 132, 0.07);
}

.funnel__testimonial-quote {
  font-size: 2.2rem;
  color: rgba(colors.$OS-RED, 0.12);
  display: block;
  margin-bottom: 0.75rem;
  line-height: 1;
}

.funnel__testimonial-text {
  font-size: 1.1rem;
  color: colors.$OS-DARK;
  line-height: 1.65;
  margin: 0 0 1.5rem;
  font-style: italic;
}

.funnel__testimonial-author {
  display: flex;
  align-items: center;
  gap: 0.75rem;

  strong { display: block; color: colors.$OS-DARK; font-size: 0.88rem; font-weight: 700; }
  span { font-size: 0.78rem; color: #8A9BB5; }
}

.funnel__testimonial-avatar {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background: colors.$OS-RED;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  i { color: #ffffff; font-size: 1.2rem; }
}

// ── Authority ────────────────────────────────────────────────────────────────
.funnel__authority {
  padding: 4rem 0;
  background: linear-gradient(135deg, #F0FFF8 0%, #F0FFF8 100%);
  border-top: 1px solid #D1FAE5;
  border-bottom: 1px solid #D1FAE5;
}

.funnel__authority-inner {
  display: flex;
  gap: 2.5rem;
  align-items: flex-start;
  @media (max-width: 640px) { flex-direction: column; align-items: center; }
}

.funnel__authority-photo-wrap {
  flex-shrink: 0;
  @media (max-width: 640px) { width: 100%; }
}

.funnel__authority-photo-frame {
  width: 260px;
  border-radius: 20px;
  overflow: hidden;
  box-shadow: 0 20px 60px rgba(22, 199, 132, 0.2), 0 4px 16px rgba(0,0,0,0.06);
  border: 3px solid rgba(22, 199, 132, 0.12);
  @media (max-width: 640px) { width: 100%; max-width: 280px; margin: 0 auto; }
}

.funnel__authority-img {
  width: 100%;
  height: 340px;
  object-fit: cover;
  object-position: top center;
  display: block;
}

.funnel__authority-content { flex: 1; }

.funnel__authority-eyebrow {
  font-family: fonts.$font-interface;
  font-size: 0.76rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: colors.$OS-BLUE;
  margin: 0 0 0.35rem;
}

.funnel__authority-name {
  @include fonts.heading-font(800);
  font-size: 2rem;
  color: colors.$OS-DARK;
  margin: 0 0 0.2rem;
  letter-spacing: -0.02em;
}

.funnel__authority-role {
  font-size: 0.88rem;
  color: #8A9BB5;
  margin: 0 0 1rem;
}

.funnel__authority-bio {
  font-size: 0.93rem;
  color: #3A4F6A;
  line-height: 1.65;
  margin: 0 0 1rem;
  strong { color: colors.$OS-DARK; font-weight: 700; }
}

.funnel__authority-creds {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;

  li {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.86rem;
    color: #3A4F6A;
    i { color: colors.$OS-RED; font-size: 0.82rem; flex-shrink: 0; }
  }
}

// ── CTA Final ────────────────────────────────────────────────────────────────
.funnel__cta-final {
  padding: 4.5rem 0;
  background: colors.$OS-NAVY;
  text-align: center;

  .funnel__section-label { color: rgba(#ffffff, 0.5); }

  .funnel__cta-btn {
    margin: 0 auto 1rem;
    background: colors.$OS-RED;
    box-shadow: 0 4px 24px rgba(22, 199, 132, 0.4);
    &:hover { background: darken(#16C784, 8%); }
  }

  .funnel__cta-sub { color: rgba(#ffffff, 0.5); }
}

.funnel__cta-final-title {
  @include fonts.heading-font(800);
  font-size: clamp(1.8rem, 4vw, 2.8rem);
  color: #ffffff;
  margin: 0 0 0.75rem;
  letter-spacing: -0.025em;
}

.funnel__cta-final-sub {
  font-size: 0.97rem;
  color: rgba(#ffffff, 0.72);
  margin: 0 auto 2rem;
  max-width: 520px;
  line-height: 1.55;
}

// ── Footer ───────────────────────────────────────────────────────────────────
.funnel__footer {
  background: colors.$OS-DARK;
  padding: 2rem 1.5rem;
}

.funnel__footer-inner {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  text-align: center;
}

.funnel__footer-logo {
  height: 30px;
  width: auto;
  filter: brightness(100);
  opacity: 0.6;
  object-fit: contain;
}

.funnel__footer-links {
  display: flex;
  gap: 1.5rem;

  a {
    font-size: 0.78rem;
    color: rgba(#ffffff, 0.45);
    text-decoration: none;
    transition: color 0.2s;
    &:hover { color: rgba(#ffffff, 0.85); }
  }
}

.funnel__footer-copy {
  font-size: 0.72rem;
  color: rgba(#ffffff, 0.28);
  margin: 0;
}
</style>
