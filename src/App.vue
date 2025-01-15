<script setup>
import { ref, onMounted } from 'vue';
import { gsap } from 'gsap';

import PlotChart from './components/PlotChart.vue';
import PieDonutChart from './components/PieDonutChart.vue';
import BarChart from './components/BarChart.vue';
import LineChart from './components/LineChart.vue';
import ScatterChart from './components/ScatterChart.vue';
import Test from './components/Test.vue';
import PriceSection from './sections/PriceSection.vue';
import CorrelationSection from './sections/CorrelationSection.vue';
import VolumSection from './sections/VolumSection.vue';

onMounted(() => {
  const tl = gsap.timeline();

  tl.to('body', { duration: 0.5, opacity: 1 })
    .to('.topleft', { duration: 0.75, left: '40px', ease: 'power2.out' }, '-=0.5')
    .to('.bottomleft-div', { duration: 0.75, left: '40px', ease: 'power2.out' }, '-=0.75')
    .to('.bottomright', { duration: 0.75, right: '60px', ease: 'power2.out' }, '-=0.75')
    .to('.topleft', { duration: 0.25, opacity: 0, ease: 'power2.out' }, '+=3')
    .to('.bottomleft-div', { duration: 0.25, opacity: 0, ease: 'power2.out' }, '-=0.25')
    .to('.bottomright', { duration: 0.25, opacity: 0, ease: 'power2.out' }, '-=0.25')
    .to('.intro-section', { duration: 0.5, padding: '20px', gap: '30px', ease: 'power2.out' })
    .to('.overlay-picture', { duration: 0.75, height: '70%', borderRadius: '15px', ease: 'ease' }, '-=0.5');

  const sections = document.querySelectorAll('section');
  const navLinks = document.querySelectorAll('nav p');

  const observer = new IntersectionObserver((entries) => {
    let anySectionIntersecting = false;
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        anySectionIntersecting = true;
        navLinks.forEach(link => {
          link.classList.remove('active');
          if (link.textContent.toLowerCase() === entry.target.dataset.section) {
            link.classList.add('active');
          }
        });
      }
    });

    if (!anySectionIntersecting) {
      navLinks.forEach(link => {
        link.classList.remove('active');
        if (link.textContent.toLowerCase() === 'Volum') {
          link.classList.add('active');
        }
      });
    }
  }, { threshold: 0.5 });

  sections.forEach(section => {
    observer.observe(section);
  });
});
</script>

<template>
  <main class="main-section">
    <section class="intro-section">
      <section class="overlay-picture">
        <div class="background-picture">
          <p class="topleft">Avokado</p>

          <p class="bottomright">Analyse</p>

          <div class="bottomleft-div">
            <p class="top">Fra middelmådighet til stjernestatus</p>
            <p class="bottom">Avokadosalg, pris og diverse</p>
          </div>
        </div>
      </section>
      <section class="text-section">
        <p class="header">Intro</p>
        <p>Avokado er en grønnsak som i det seine 2010 ble ekstremt populert og en del av mange sitt kosthold, men 
          hvor populært ble det faktisk? Hvor masse har blitt solgt, hvilke område har mest salg og kan dette forklares?
        </p>
      </section>
    </section>

    <nav>
      <p>Volum</p> <!-- totaltvolum over tid -->
       <p>Salg</p> <!-- salg over tid -->
       <p>Korrelasjon</p> <!-- Korrelasjon mellom pris, volum og størrelse -->
    </nav>
    
    <VolumSection class="volum-section" data-section="volum" />
    <PriceSection class="price-section" data-section="salg" />
    <CorrelationSection class="correlation-section" data-section="korrelasjon" />

    <!-- <PlotChart /> -->
    <!-- <PieDonutChart /> -->
    <!-- <BarChart /> -->
    <!-- <LineChart /> -->
    <!-- <ScatterChart /> -->
    <!-- <Test /> -->
  </main>
</template>

<style lang="scss">
  .main-section {
    width: 100%;
    position: relative;
    
    nav {
      position: sticky;
      z-index: 999;
      top: 0;

      width: 100%;
      margin-top: 10dvh;
      padding: 20px;

      display: flex;
      flex-direction: row;
      align-items: center;
      gap: 40px;




      p {
        font-size: 2.5rem;
        padding: 30px 0 0;
        cursor: pointer;
        font-weight: 200;
        color: #707070;
        transition: all 500ms ease;
        transition: font-weight 250ms ease;

        &.active {
          color: #1D1D1D;
          border-top: 2px solid #1D1D1D;
          font-weight: 800;
          font-style: italic;
        }
      }
    }

    .intro-section {
      width: 100%;
      height: 100dvh;
      overflow: hidden;

      // padding: 20px;

      display: flex;
      flex-direction: column;
      // gap: 20px;

      .overlay-picture {
        width: 100%;
        height: 100%;
        overflow: hidden;

        // height: 70%;
        // border-radius: 15px;


        .background-picture {
          width: 100dvw;
          height: 100dvh;
          position: relative;

          background-image: url('https://images.pexels.com/photos/259280/pexels-photo-259280.jpeg?cs=srgb&dl=pexels-pixabay-259280.jpg&fm=jpg');
          background-position: center;
          background-size: cover;

          display: flex;

          .topleft {
            position: absolute;
            top: 30px;
            left: -50%;
            // left: 40px;
            
            font-size: 10rem;
            font-weight: 900;
          }
          .bottomleft-div {
            position: absolute;
            bottom: 60px;
            left: -50%;
            // left: 40px;

            display: flex;
            flex-direction: column;
            gap: 0px;

            p {
              font-size: 2.5rem;
              color: white;
            }
            .top {
              font-weight: 800;
            }
            .bottom {
              font-size: 2rem;
              font-weight: 300;
            }
          }
          .bottomright {
            position: absolute;
            bottom: 30px;
            // right: 60px;
            right: -50%;
            
            color: white;
            font-size: 10rem;
            font-weight: 100;
            font-style: italic;
          }
        }
      }

      .text-section {
        width: 100%;
        flex: 1;
        overflow: hidden;

        display: flex;
        flex-direction: column;

        p {
          font-family: "Montserrat", serif;
          font-size: 2.5rem;

          &.header {
            font-size: 4rem;
            font-weight: 800;
          }
        }
      }
    }

    .price-section, .volum-section, .correlation-section {
      min-height: 100dvh;
      width: 100dvw;
      padding: 20px;
    }
    .volum-section {
    }
    .price-section {
    display: grid;
      place-items: center;
    }
    .correlation-section {
      display: grid;
      place-items: center;
    }
  }
</style>