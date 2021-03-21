<template>
  <div class="wrap">
    <section id="intro-section" :ref="sectionNames[0]" class="paral-section">
      <div class="intro-box" :style="txtBoxStyling">
        <div class="intro-bg"></div>
      </div>
    </section>
    <section :ref="sectionNames[1]" class="paral-section">
      <div class="busniesscard_container" :style="busniesscardStyling">
        <div class="busniesscard_top_bg"></div>
        <div class="busniesscard_bottom_bg"></div>
        <div class="busniesscard_wrap">
          <div class="busniesscard_box" :class="rotateDevice">
            <div class="busniesscard_face front"></div>
            <div class="busniesscard_face back"></div>
          </div>
        </div>
      </div>
    </section>
    <section :ref="sectionNames[2]" class="paral-section"></section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      sectionNames: ['paral-section-1', 'paral-section-2', 'paral-section-3'],
      scrollY: 0,
      offsetTop: [],
      offsetBottom: [],
      activeIndex: null,
      activeSectionPer: 0,
    }
  },
  computed: {
    txtBoxStyling() {
      const display =
        this.activeIndex === 0 || this.activeIndex === null ? 'block' : 'none'
      const opacity = 1 - this.activeSectionPer

      return {
        display,
        opacity,
      }
    },
    busniesscardStyling() {
      switch (this.activeIndex) {
        case 1:
          return {
            display: 'block',
            opacity: this.activeSectionPer * 3.5,
          }
        case 2:
          return {
            display: 'block',
            opacity: 1,
          }
        default:
          return {
            display: 'none',
            opacity: 0,
          }
      }
    },
    rotateDevice() {
      // console.log('rotateDevice', this.activeIndex)
      switch (this.activeIndex) {
        case 2:
          return 'back'
        default:
          return ''
      }
    },
  },
  watch: {
    scrollY(newValue, oldValue) {
      this.setActiveIndex()
      this.setActiveSectionPer()
    },
  },
  mounted() {
    window.addEventListener('scroll', this.handleScroll)
    this.setValue()
    this.$nextTick(() => {
      window.addEventListener('resize', this.handleResize)
    })
  },
  beforeDestroy() {
    window.removeEventListener('scroll', this.handleScroll)
    window.removeEventListener('resize', this.handleResize)
  },
  methods: {
    handleScroll() {
      this.scrollY = window.scrollY
    },
    handleResize() {
      this.setValue()
    },
    setActiveSectionPer() {
      const winScrollTop = this.scrollY
      const offsetTop = this.offsetTop[this.activeIndex]
      const offsetBottom = this.offsetBottom[this.activeIndex]

      const activeSectionPer =
        1 - (offsetBottom - (winScrollTop - offsetTop)) / offsetBottom

      this.activeSectionPer = 1 * activeSectionPer
    },
    setActiveIndex() {
      const winScrollTop = this.scrollY
      this.sectionNames.forEach((sectionName, index) => {
        if (
          winScrollTop >= this.offsetTop[index] &&
          this.offsetBottom[index] > winScrollTop
        ) {
          this.activeIndex = index
        }
      })
    },
    setValue() {
      window.scrollTop = 0
      this.sectionNames.forEach((sectionName, index) => {
        const sectionRef = this.$refs[sectionName]
        this.offsetTop[index] = sectionRef.offsetTop
        this.offsetBottom[index] =
          sectionRef.offsetTop + sectionRef.clientHeight
      })
    },
  },
}
</script>

<style lang="scss">
@import '~@/assets/scss/site.scss';
</style>
