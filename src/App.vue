<template>
  <div class="top">
    <header>
      <div class="container">
        <img src="./assets/images/logo.svg" alt="logo" />
        <ul v-if="!isMobile" class="menu">
          <li>Features</li>
          <li>Pricing</li>
          <li>Resources</li>
        </ul>
        <div class="buttons" v-if="!isMobile">
          <span class="log">Login</span>
          <span class="sign">Sign Up</span>
        </div>
        <img
          src="./assets/images/menu-icon.svg"
          v-if="isMobile"
          class="menuIcon"
          @click="showMenu = !showMenu"
        />
        <ul v-if="menu" class="mobileMenu">
          <li>Features</li>
          <li>Pricing</li>
          <li>Resources</li>
          <li class="log">Login</li>
          <li class="sign">Sign Up</li>
        </ul>
      </div>
    </header>
    <section class="landing">
      <div class="container">
        <div class="content">
          <h1>More than just shorter links</h1>
          <p>
            Build your brand's recognition and get detailed insights
            on how your links are performing.
          </p>
          <a href="#tool" class="start">Get Started</a>
        </div>
        <img src="./assets/images/illustration-working.svg" alt="work" />
      </div>
    </section>
  </div>
  <main>
    <div class="container">
      <section class="tool" id="tool">
        <div class="form">
          <div class="input">
            <input
              type="text"
              v-model="linkInput"
              placeholder="Shorten a link here..."
              @keyup.enter="makeLink"
            />
          </div>
          <span @click="makeLink">Shorten It</span>
        </div>
        <div class="links">
          <LinkBox
            v-for="(e , i) in links"
            :key="i"
            :origin="e.origin"
            :short="e.short"
            @copied="removeClass"
          />
        </div>
      </section>
      <section class="stats">
        <div class="head">
          <h2>Advanced Statistics</h2>
          <p>
            Track how your links are performing across the web with our
            advanced statistics dashboard.
          </p>
        </div>
        <div class="content">
          <StatBox
            v-for="(e , i) in stats"
            :class="`stat stat-${i + 1}`"
            :key="i"
            :title="e.title"
            :content="e.description"
            :img="e.img"
          />
        </div>
      </section>
    </div>
  </main>
  <section class="boost">
    <h2>Boost your links today</h2>
    <span href="#tool" class="start">Get Started</span>
  </section>
  <footer>
    <div class="container">
      <div class="logo">
        <img src="./assets/images/logo.svg" alt="logo" />
      </div>
      <div class="lists">
        <ul>
          <li>Features</li>
          <li>Link Shortening</li>
          <li>Branded Links</li>
          <li>Analytics</li>
        </ul>
        <ul>
          <li>Resources</li>
          <li>Blog</li>
          <li>Developers</li>
          <li>Support</li>
        </ul>
        <ul>
          <li>Company</li>
          <li>About</li>
          <li>Our Team</li>
          <li>Careers</li>
          <li>Contact</li>
        </ul>
      </div>
      <ul class="links">
        <li>
          <img src="./assets/images/icon-facebook.svg" />
        </li>
        <li>
          <img src="./assets/images/icon-twitter.svg" />
        </li>
        <li>
          <img src="./assets/images/icon-pinterest.svg" />
        </li>
        <li>
          <img src="./assets/images/icon-instagram.svg" />
        </li>
      </ul>
    </div>
  </footer>
</template>

<script>

import LinkBox from './components/linkBox.vue'
import StatBox from './components/statBox.vue'

export default {
  data() {
    return {
      showMenu:false,
      isMobile: false,
      window: null,
      linkInput: "",
      links: [],
      stats: [
        {
          title: 'Brand Recognition',
          description: `
            Boost your brand recognition with each click.
            Generic links don't mean a thing.
            Branded links help instil confidence in your content.`,
          img: 'icon-brand-recognition.svg'
        },
        {
          title: 'Detailed Records',
          description: `
            Gain insights into who is clicking your links.
            Knowing when and where people engage with your 
            content helps inform better decisions.`,
          img: 'icon-detailed-records.svg'
        },
        {
          title: 'Fully Customizable',
          description: `
            Improve brand awareness and content 
            discoverability through customizable 
            links, supercharging audience engagement.`,
          img: 'icon-fully-customizable.svg'
        }
      ],
      //eslint-disable-next-line
      urlPattern: /[(http(s)?):\/\/(www\.)?a-zA-Z0-9@:%._\+~#=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#?&//=]*)/
    }
  },
  methods: {
    async makeLink() {
      if (!this.linkInput.length) {
        document.querySelector('.input').classList.add("invalid")
      } else if (!this.urlPattern.test(this.linkInput)) {
        document.querySelector('.input').classList.add("invalid")
      } else {
        document.querySelector('.input').classList.remove("invalid")

        console.log(this.linkInput)

        let req = await fetch(`https://api.shrtco.de/v2/shorten?url=${this.linkInput}`)
        let res = await req.json()

        console.log(res)

        if (this.links.length === 5) this.links.pop()

        this.links.unshift({
          "origin": this.linkInput,
          "short": res.result.short_link
        })

        this.linkInput = ""

        localStorage.setItem("links", JSON.stringify(this.links))
      }
    },
    removeClass() {
      let buttons = document.querySelectorAll(".tool .link .copy")

      buttons.forEach((e) => {
        e.classList.remove("active")
      })
    },
    checkScreen() {
      this.window = window.innerWidth;
      if (this.window <= 767) {
        this.isMobile = true
        return;
      }
      this.isMobile = false
      this.showMenu = false
    }
  },
  computed:{
    menu(){
      if (this.showMenu && this.isMobile){
        return true
      }
      return false
    }
  },
  mounted() {
    if (localStorage.getItem("links")) {
      this.links = JSON.parse(localStorage.getItem("links"))
    }
    window.addEventListener('resize', this.checkScreen)
    this.checkScreen()
  },
  components: { LinkBox, StatBox }
}
</script>
