<script setup lang="ts">
import { onBeforeUnmount, onMounted, ref } from "vue";
import HeroModel from "./components/HeroModel.vue";
import "./assets/main.css";
import "./assets/hero-fix.css";

const dark = ref(false);
const menuOpen = ref(false);
const activeSlide = ref(0);
let slideTimer: ReturnType<typeof setInterval> | undefined;
onMounted(() => {
  slideTimer = setInterval(() => {
    activeSlide.value = (activeSlide.value + 1) % 3;
  }, 13000);
});
onBeforeUnmount(() => clearInterval(slideTimer));
const services = [
  [
    "01",
    "งานถนนและคอนกรีต",
    "ถนนภายในโรงงาน ลานจอด ลานคอนกรีตเสริมเหล็ก และระบบระบายน้ำ",
  ],
  [
    "02",
    "ราวกันตกและงาน Safety",
    "ราวกันตก ราวกันชน โครงเหล็ก บันได และทางเดินตามมาตรฐานโรงงาน",
  ],
  [
    "03",
    "ติดตั้งกล้องวงจรปิด",
    "ออกแบบจุดติดตั้ง เดินสาย วางระบบบันทึกภาพ และดูแลต่อเนื่อง",
  ],
  [
    "04",
    "อาคารและโครงสร้าง",
    "สำนักงาน โกดัง ต่อเติมพื้นที่ผลิต พร้อมงานโครงสร้างและหลังคา",
  ],
];
const projects = [
  ["/project-01.png", "งานโครงสร้างและปรับปรุงโรงงาน"],
  ["/project-02.png", "งานพื้นและถนนคอนกรีต"],
  ["/project-03.png", "ระบบความปลอดภัยหน้างาน"],
  ["/project-04.png", "งานติดตั้งและซ่อมบำรุง"],
];
</script>

<template>
  <div :class="['site', { light: !dark }]">
    <header class="header">
      <a class="brand" href="#top"
        ><span class="brand-mark">RKS</span
        ><span
          ><b>R.A.K.S. <em>ENGINEERING</em></b
          ><small>บริษัท อาร์.เอ.เค.เอส. เอ็นจิเนียริ่ง จำกัด</small></span
        ></a
      >
      <nav :class="{ open: menuOpen }">
        <a href="#services" @click="menuOpen = false">บริการ</a
        ><a href="#about" @click="menuOpen = false">เกี่ยวกับเรา</a
        ><a href="#works" @click="menuOpen = false">ผลงาน</a
        ><a href="#contact" @click="menuOpen = false">ติดต่อเรา</a>
      </nav>
      <div class="header-actions">
        <button class="theme" @click="dark = !dark">
          {{ dark ? "☀" : "◐" }}</button
        ><a class="quote compact" href="#contact">ขอใบเสนอราคา</a
        ><button class="menu" @click="menuOpen = !menuOpen">☰</button>
      </div>
    </header>

    <main>
      <section id="top" class="hero">
        <HeroModel :slide="activeSlide" />
        <div class="hero-copy">
          <p class="eyebrow">
            <i></i> บริษัท อาร์.เอ.เค.เอส. เอ็นจิเนียริ่ง จำกัด
          </p>
          <h1>งานก่อสร้าง<br /><span>ออกแบบครบวงจร</span></h1>
          <p class="lead">
            มาตรฐานระดับโรงงาน ตั้งแต่ประเมินราคา เขียนแบบ จนส่งมอบและซ่อมบำรุง
          </p>
          <div class="hero-cta">
            <a class="quote" href="#contact">ขอใบเสนอราคา</a
            ><a class="outline" href="#works">ดูผลงาน <b>→</b></a>
          </div>
          <div class="hero-dots" aria-label="เลือกสไลด์">
            <button v-for="n in 3" :key="n" :class="{ active: activeSlide === n - 1 }" @click="activeSlide = n - 1" :aria-label="`สไลด์ ${n}`"></button>
            <span>0{{ activeSlide + 1 }} / 03</span>
          </div>
        </div>
        <div class="model-hint">3D MODEL · ลากเพื่อหมุนโมเดล</div>
      </section>

      <section class="stats">
        <div>
          <b>15<em>+</em></b
          ><span>ปีประสบการณ์งานวิศวกรรม</span>
        </div>
        <div>
          <b>120<em>+</em></b
          ><span>โครงการที่ส่งมอบแล้ว</span>
        </div>
        <div><b>5</b><span>สายงานบริการหลัก</span></div>
        <div>
          <b>100<em>%</em></b
          ><span>งานภายใต้หลักวิชาชีพวิศวกรรม</span>
        </div>
      </section>

      <section id="services" class="section">
        <p class="section-tag">SERVICES</p>
        <h2>บริการของเรา</h2>
        <p class="section-intro">
          รับงานในพื้นที่โรงงานและอาคารอุตสาหกรรม
          ทำงานร่วมกับฝ่ายวิศวกรรมและฝ่ายความปลอดภัยของลูกค้าโดยตรง
        </p>
        <div class="service-grid">
          <article
            v-for="service in services"
            :key="service[0]"
            class="service-card"
          >
            <small>{{ service[0] }}</small>
            <h3>{{ service[1] }}</h3>
            <p>{{ service[2] }}</p>
            <a href="#contact">สอบถามบริการ →</a>
          </article>
        </div>
      </section>

      <section id="about" class="about">
        <div>
          <p class="section-tag">WHY RAKS</p>
          <h2>วางแผนแม่น<br />ส่งมอบตรงงาน</h2>
        </div>
        <div class="about-text">
          <p>
            เราทำงานด้วยความเข้าใจข้อจำกัดของพื้นที่จริง จึงประสานแบบ วัสดุ
            ทีมช่าง และความปลอดภัยให้เป็นแผนเดียวกัน
          </p>
          <ul>
            <li>สำรวจหน้างานและเสนอราคาอย่างชัดเจน</li>
            <li>ควบคุมคุณภาพโดยทีมงานประสบการณ์สูง</li>
            <li>ดูแลงานหลังส่งมอบ</li>
          </ul>
        </div>
      </section>

      <section id="works" class="works">
        <div class="works-head">
          <div>
            <p class="section-tag">SELECTED WORKS</p>
            <h2>ผลงานที่สร้างเสร็จ</h2>
          </div>
          <a class="outline" href="#contact">ดูโครงการเพิ่มเติม →</a>
        </div>
        <div class="project-grid">
          <figure
            v-for="(project, i) in projects"
            :key="project[1]"
            :class="{ large: i === 0 }"
          >
            <img :src="project[0]" :alt="project[1]" />
            <figcaption>
              <span>PROJECT 0{{ i + 1 }}</span
              >{{ project[1] }}
            </figcaption>
          </figure>
        </div>
      </section>

      <section id="contact" class="contact">
        <p class="section-tag">START A PROJECT</p>
        <h2>คุยกับทีมวิศวกร<br /><span>เรื่องหน้างานของคุณ</span></h2>
        <a class="quote" href="tel:+66000000000">โทรหาเรา</a
        ><a class="contact-link" href="mailto:contact@raksengineering.com"
          >contact@raksengineering.com</a
        >
      </section>
    </main>
    <footer>
      <span>© {{ new Date().getFullYear() }} R.A.K.S. ENGINEERING</span
      ><span>SAMUT SAKHON · THAILAND</span>
    </footer>
  </div>
</template>
