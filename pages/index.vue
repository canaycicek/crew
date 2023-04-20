<template>
  <div id="main">
    <div class="row w-100 p-5 secondary top m-0">
      <div class="fancy">
        <h3 class="primary-text">FL Studio</h3>
        <h3 class="primary-text">Crew</h3>
        <h3 class="primary-text">Beat</h3>
        <h3 class="primary-text">Sample</h3>
        <h3 class="primary-text">Can Ayçiçek</h3>
      </div>
      <div
        class="col-lg-4 col-12 d-flex justify-content-center align-items-center"
      >
        <div class="d-flex align-items-center">
          <app-logo style="width: 80px" />
          <div class="ms-3">
            <h1>Can Ayçiçek</h1>
            <h2 class="mb-0">Music Producer</h2>
          </div>
        </div>
      </div>
      <div class="col-lg-8 col-12">
        <h3 class="primary-text">{{ $t("about") }}</h3>
        <p>
          {{ $t("about-description") }}
        </p>
      </div>
    </div>

    <language-switcher />

    <div class="row projects secondary m-2 mt-5 mb-5 rounded">
      <div
        class="projet-title-container w-100 d-flex justify-content-center align-items-center"
      >
        <hr class="primary-border w-25 me-3" />
        <h3 class="project-title primary-text">{{ $t("projects") }}</h3>
        <hr class="primary-border w-25 ms-3" />
      </div>

      <div
        class="w-100 row pt-3"
        style="margin: 0; padding: 60px 0; margin-top: 40px"
      >
        <div
          class="col-md-12 col-sm-12 col-lg-5 col-md-12 d-flex justify-content-center"
        >
          <audio ref="audio" preload loop id="audio"></audio>
          <div>
            <div class="music-player">
              <div
                style="position: relative"
                class="d-flex justify-content-center align-items-center"
              >
                <div
                  :style="{
                    backgroundImage:
                      'url(' +
                      $router.options.base +
                      'images/' +
                      activeMusic.image +
                      ')',
                    width: '180px',
                    height: '180px',
                  }"
                  style="position: absolute; left: 15px; top: 15px; width: 100%"
                  class="rounded-circle music-image"
                  ref="musicImage"
                  :class="{
                    'not-selected': activeIndex == -1,
                  }"
                />
                <ui-circle-slider
                  class="d-flex justify-content-center align-items-center"
                  v-model="activeTime"
                  :max="activeDuration"
                  :handleSize="activeDuration == 1 ? 0 : 10"
                />
              </div>
            </div>
            <span class="text-center d-block mt-2" v-if="activeDuration == 1">{{
              activeMusic.name
            }}</span>
            <h4 class="text-center d-block mt-2" v-else>
              {{ activeMusic.name }}
            </h4>
            <span
              class="text-center d-block mt-2 text-muted"
              v-if="activeMusic.projectName"
              >({{ activeMusic.projectName }})</span
            >
            <span class="text-center d-block mt-2 text-muted"
              >{{ activeCurrentTimeClock.minute }}:{{
                activeCurrentTimeClock.second
              }}
              / {{ activeDurationClock.minute }}:{{
                activeDurationClock.second
              }}</span
            >

            <div
              class="music-controller d-flex justify-content-between align-items-center pt-4"
            >
              <font-awesome-icon
                :icon="['fas', 'backward']"
                :class="{ 'text-muted': checkIsFirstProject() }"
                @click="previousProject"
              />
              <div>
                <font-awesome-icon
                  :icon="['fas', 'step-backward']"
                  class="me-3"
                  :class="{ 'text-muted': checkIsFirstMusic() }"
                  @click="previousMusic"
                />
                <font-awesome-icon
                  :icon="['fas', 'pause']"
                  v-if="checkMusicIsPlaying()"
                  @click="toggleSound()"
                  class="me-3"
                />
                <font-awesome-icon
                  :icon="['fas', 'play']"
                  v-else
                  class="me-3"
                  @click="toggleSound()"
                />

                <font-awesome-icon
                  :icon="['fas', 'step-forward']"
                  :class="{ 'text-muted': checkIsLastMusic() }"
                  @click="nextMusic"
                />
              </div>
              <font-awesome-icon
                :icon="['fas', 'forward']"
                :class="{ 'text-muted': checkIsLastProject() }"
                @click="nextProject"
              />
            </div>
            <template v-if="activeMusic.social">
              <hr class="mt-5" />
              <!-- column -->
              <div
                class="d-flex justify-content-between flex-column gap-4 align-items-center"
              >
                <template
                  v-for="(socialUrl, socialIndex) in activeMusic.social"
                >
                  <a
                    :key="socialIndex"
                    v-if="socialIndex < 3 || showMoreSocial"
                    target="_blank"
                    :href="socialUrl"
                    class="d-flex justify-content-between align-items-center w-100"
                  >
                    <span>
                      <font-awesome-icon
                        :icon="['fab', getSocialIcon(socialUrl)]"
                        class="me-1"
                      />
                      {{ getName(socialUrl) }}'da dinle
                    </span>
                    <font-awesome-icon
                      :icon="['fas', 'external-link-alt']"
                      class="ms-1"
                    />
                  </a>
                </template>
                <span
                  v-if="!showMoreSocial"
                  class="text-muted clickable"
                  @click="showMoreSocial = true"
                  >{{ $t("more") }}...</span
                >
              </div>
            </template>
          </div>
        </div>
        <div class="col-md-12 col-sm-12 col-lg-7 musics ps-5 pe-4">
          <div class="accordion">
            <div
              class="accordion-item mt-4"
              v-for="(project, projectIndex) in projects"
              :key="projectIndex"
            >
              <h2 class="accordion-header">
                <button
                  class="accordion-button"
                  :class="{ collapsed: projectIndex !== 0 }"
                  type="button"
                  data-bs-toggle="collapse"
                  :data-bs-target="'#collapse' + projectIndex"
                  aria-expanded="true"
                  :aria-controls="'#collapse' + projectIndex"
                >
                  {{ project.title }} ({{ project.musics.length }})
                </button>
              </h2>

              <!-- musics -->
              <div
                class="ps-5 accordion-collapse collapse"
                :class="{ show: projectIndex === 0 }"
                :id="'collapse' + projectIndex"
              >
                <project-music
                  v-for="(music, musicIndex) in project.musics"
                  :key="musicIndex"
                  :music="music"
                  @click="changeMusic(music, musicIndex, projectIndex)"
                  :active="
                    activeIndex === musicIndex &&
                    activeProjectIndex === projectIndex
                  "
                />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <footer class="secondary mt-5 row p-5 w-100 m-0">
      <div class="col-12 col-lg-8 p-4">
        <div class="mb-4">
          <span class="primary-text title">{{ this.$t("contact") }}</span>
          <p>
            {{ this.$t("contact-description") }}
          </p>
        </div>

        <div v-for="(item, index) in contactInfo" :key="index">
          <span class="primary-text subtitle">{{ item.title }}</span>
          <a
            v-for="(content, contentIndex) in item.content"
            :key="contentIndex"
            :href="content.link"
            class="text-muted"
            >{{ content.text }}</a
          >
        </div>
      </div>

      <div
        class="col-12 col-lg-4 d-flex align-items-center flex-column gap-4 p-4"
      >
        <span class="primary-text title">{{ this.$t("social-media") }}</span>
        <!-- get array keys "mine" in sOcialMediaAccounts -->
        <template v-for="(socialUrl, socialIndex) in socialMediaLinks()">
          <a
            :key="socialIndex"
            target="_blank"
            :href="socialUrl"
            class="bg d-flex justify-content-between align-items-center w-75 p-3 rounded"
          >
            <span>
              <font-awesome-icon
                :icon="['fab', getSocialIcon(socialUrl)]"
                class="me-1"
              />
              {{ getName(socialUrl) }}
            </span>
          </a>
        </template>
      </div>
    </footer>
  </div>
</template>
<script>
import projectDatas from "../data/projects.json";
import socialMediaAccounts from "../data/social.json";
import contactInfo from "../data/contact.json";
import LanguageSwitcher from "../components/LanguageSwitcher.vue";

export default {
  components: { LanguageSwitcher },
  data() {
    return {
      projects: projectDatas,
      activeMusic: {
        image: "default-project-image.png",
        music: "fear.mp3",
        name: this.$t("not-selected-music"),
      },
      activeIndex: -1,
      activeProjectIndex: -1,
      activeDuration: 1,
      activeDurationClock: {
        minute: "00",
        second: "00",
      },
      activeCurrentTime: 0,
      activeCurrentTimeClock: {
        minute: "00",
        second: "00",
      },
      showMoreSocial: false,
      socialMediaAccounts,
      contactInfo,
    };
  },
  computed: {
    activeTime: {
      get() {
        return this.activeCurrentTime;
      },
      set(value) {
        this.activeCurrentTime = value;
        this.$refs.audio.currentTime = value;
      },
    },
  },
  methods: {
    socialMediaLinks() {
      return this.socialMediaAccounts
        .map((social) => social.mine)
        .filter((social) => social);
    },
    getDomain(url) {
      let domain = new URL(url);
      domain = domain.hostname;
      domain = domain.replace("www.", "");
      console.log({ domain });
      return domain;
    },
    getName(url) {
      const domain = this.getDomain(url);
      return this.socialMediaAccounts.find((social) =>
        social.domains.includes(domain)
      ).name;
    },
    getSocialIcon(url) {
      const domain = this.getDomain(url);
      return this.socialMediaAccounts.find((social) =>
        social.domains.includes(domain)
      ).icon;
    },
    toggleSound(target = null) {
      console.log("toggle sound");
      let audio = this.$refs.audio;

      if (target) {
        if (target == "play") {
          if (audio.paused) {
            audio.play();
          } else {
            audio.pause();
            audio.play();
          }
        }
        return;
      }

      if (audio.paused) {
        console.log("play it");
        audio.play();
      } else {
        console.log("pause it");
        audio.pause();
      }
    },
    secondsToClock(givenSeconds) {
      const minutes = Math.floor(givenSeconds / 60)
        .toString()
        .padStart(2, "0");
      const seconds = Math.floor(givenSeconds % 60)
        .toString()
        .padStart(2, "0");
      return { minute: minutes, second: seconds };
    },
    updateSound() {
      let audio = this.$refs.audio;
      audio.src = "audio/" + this.activeMusic.music;
      audio.load();
      audio.addEventListener("canplaythrough", (event) => {
        console.log({ audio: audio.duration });
        this.activeDuration = audio.duration;
        this.activeDurationClock = this.secondsToClock(audio.duration);
        audio.addEventListener("timeupdate", (event) => {
          this.activeCurrentTime = audio.currentTime;
          this.activeCurrentTimeClock = this.secondsToClock(audio.currentTime);
        });
        this.toggleSound("play");
      });
    },
    _restartMusic() {
      let audio = this.$refs.audio;
      audio.currentTime = 0;
      this.toggleSound("play");
    },
    changeMusic(music, musicIndex, projectIndex) {
      this.showMoreSocial = false;
      if (
        this.activeIndex === musicIndex &&
        this.activeProjectIndex === projectIndex
      ) {
        this._restartMusic();
        return;
      }
      this.activeIndex = musicIndex;
      this.activeProjectIndex = projectIndex;
      this.activeMusic = {
        ...music,
        musicIndex: musicIndex,
        projectIndex,
        projectName: this.projects[projectIndex].title,
        maxProject: this.projects.length - 1,
        maxMusic: this.projects[projectIndex].musics.length - 1,
      };
      this.updateSound();
    },
    nextProject() {
      const nextProjectIndex = this.activeProjectIndex + 1;
      const nextMusicIndex = 0;
      this.changeMusic(
        this.projects[nextProjectIndex].musics[0],
        nextMusicIndex,
        nextProjectIndex
      );
    },
    nextMusic() {
      const nextMusicIndex = this.activeMusic.musicIndex + 1;
      const activeProjectIndex = this.activeProjectIndex;
      this.changeMusic(
        this.projects[this.activeProjectIndex].musics[nextMusicIndex],
        nextMusicIndex,
        activeProjectIndex
      );
    },
    previousProject() {
      const nextMusicIndex = 0;
      const nextProjectIndex = this.activeProjectIndex - 1;
      this.changeMusic(
        this.projects[nextProjectIndex].musics[0],
        nextMusicIndex,
        nextProjectIndex
      );
    },
    previousMusic() {
      const nextMusicIndex = this.activeMusic.musicIndex - 1;
      const activeProjectIndex = this.activeProjectIndex;
      this.changeMusic(
        this.projects[activeProjectIndex].musics[nextMusicIndex],
        nextMusicIndex,
        activeProjectIndex
      );
    },

    checkIsLastMusic() {
      return this.activeMusic.musicIndex === this.activeMusic.maxMusic;
    },
    checkIsLastProject() {
      return this.activeProjectIndex === this.activeMusic.maxProject;
    },
    checkIsFirstMusic() {
      return !this.activeMusic.musicIndex > 0;
    },
    checkIsFirstProject() {
      return !this.activeProjectIndex > 0;
    },
    checkMusicIsPlaying() {
      let audio = this.$refs.audio;
      return audio && !audio.paused;
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Satisfy&display=swap");
@import "../node_modules/@fortawesome/fontawesome-svg-core/styles.css";
:root {
  --primary-color: #80ff00;
  --secondary-color: #202020;
  --gray: #262626;
  --dark: #0f0f0f;

  --social-youtube: #ff0000;
  --social-instagram: #c13584;
  --social-facebook: #3b5998;
  --social-apple: #939393;
  --social-spotify: #1db954;
  --social-soundcloud: #ff8800;
  --social-deezer: #ff0000;
  --social-tiktok: #69c9d0;
}

body {
  background-color: #121212;
  color: white;
}

#main > div:not(:last-child) {
  margin-bottom: 8rem !important;
}

h1 {
  font-family: "Satisfy", cursive;
  font-size: 2.2rem;
}

h2 {
  font-size: 1.1rem;
}

a {
  color: white;
  text-decoration: none;
}

a:hover {
  color: inherit;
  text-decoration: none;
}

.clickable {
  cursor: pointer;
}

a:has(svg[data-icon="youtube"]):not(.bg):hover {
  color: var(--social-youtube);
}

a:has(svg[data-icon="instagram"]):not(.bg):hover {
  color: var(--social-instagram);
}

a:has(svg[data-icon="facebook"]):not(.bg):hover {
  color: var(--social-facebook);
}

a:has(svg[data-icon="apple"]):not(.bg):hover {
  color: var(--social-apple);
}

a:has(svg[data-icon="spotify"]):not(.bg):hover {
  color: var(--social-spotify);
}

a:has(svg[data-icon="soundcloud"]):not(.bg):hover {
  color: var(--social-soundcloud);
}

a:has(svg[data-icon="deezer"]):not(.bg):hover {
  color: var(--social-deezer);
}

a:has(svg[data-icon="tiktok"]):not(.bg):hover {
  color: var(--social-tiktok);
}

a:has(svg[data-icon="youtube"]).bg {
  background-color: var(--social-youtube);
}

a:has(svg[data-icon="instagram"]).bg {
  background-color: var(--social-instagram);
}

a:has(svg[data-icon="facebook"]).bg {
  background-color: var(--social-facebook);
}

a:has(svg[data-icon="apple"]).bg {
  background-color: var(--social-apple);
}

a:has(svg[data-icon="spotify"]).bg {
  background-color: var(--social-spotify);
}

a:has(svg[data-icon="soundcloud"]).bg {
  background-color: var(--social-soundcloud);
}

a:has(svg[data-icon="deezer"]).bg {
  background-color: var(--social-deezer);
}

a:has(svg[data-icon="tiktok"]).bg {
  background-color: var(--social-tiktok);
}

.transition {
  transition: all 0.3s;
}

.accordion-button {
  color: white;
  background-color: var(--dark);
  border: none;
}

.accordion-button:focus {
  box-shadow: none;
  border: none;
}

.accordion-button.collapsed::after {
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='%23fff'%3e%3cpath fill-rule='evenodd' d='M1.646 4.646a.5.5 0 0 1 .708 0L8 10.293l5.646-5.647a.5.5 0 0 1 .708.708l-6 6a.5.5 0 0 1-.708 0l-6-6a.5.5 0 0 1 0-.708z'/%3e%3c/svg%3e");
}

.accordion-button:not(.collapsed) {
  background-color: var(--dark);
  color: white;
}

.accordion-button:not(.collapsed)::after {
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='%23fff'%3e%3cpath fill-rule='evenodd' d='M1.646 4.646a.5.5 0 0 1 .708 0L8 10.293l5.646-5.647a.5.5 0 0 1 .708.708l-6 6a.5.5 0 0 1-.708 0l-6-6a.5.5 0 0 1 0-.708z'/%3e%3c/svg%3e");
}

.primary {
  background-color: var(--primary-color);
}

.secondary {
  background-color: var(--secondary-color);
}

.dark {
  background-color: var(--dark);
}

.gray {
  background-color: var(--gray);
}

.primary-text {
  color: var(--primary-color);
}

.secondary-text {
  color: var(--secondary-color);
}

.dark-text {
  color: var(--dark);
}

.gray-text {
  color: var(--gray);
}

.primary-border {
  border: 1px solid var(--primary-color);
}

.secondary-border {
  border: 1px solid var(--secondary-color);
}

.dark-border {
  border: 1px solid var(--dark);
}

.gray-border {
  border: 1px solid var(--gray);
}

.projects {
  position: relative;
  width: 85%;
  margin-left: 7.5% !important;
  background: linear-gradient(45deg, black, transparent, transparent);
}

.project-head {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.projet-title-container {
  position: absolute;
  top: -18px;
  left: 0;
}

.music-image {
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;
}
.music-image.not-selected {
  box-shadow: 1px 1px 3px var(--primary-color);
}

.musics {
  border-left: 1px solid rgb(107, 107, 107);
  max-height: 500px;
  overflow-y: scroll;
}

.musics::-webkit-scrollbar {
  width: 2px;
  background: white;
}

.musics::-webkit-scrollbar-track {
  background: var(--dark);
}

.musics::-webkit-scrollbar-thumb {
  background: var(--primary-color);
}

.music-controller svg {
  cursor: pointer;
  width: 20px;
}

.top {
  clip-path: polygon(0 0, 100% 0, 100% 75%, 0% 100%);
  position: relative;
  padding-bottom: 8rem !important;
  margin-bottom: 10rem !important;
}

.top:before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(
    170deg,
    rgba(0, 0, 0, 0) 0%,
    rgba(0, 0, 0, 0) 0%,
    rgba(0, 0, 0, 0.1) 0%,
    rgba(0, 0, 0, 0.8) 100%
  );
}

footer {
  border-bottom: 1px solid var(--primary-color);
  background: linear-gradient(22deg, black, transparent);
}

footer .title {
  font-size: 2rem;
  font-weight: bold;
  display: block;
}

footer .subtitle {
  font-size: 1.5rem;
  font-weight: bold;
  display: block;
}

.fancy {
  height: 100%;
  width: 100%;
}

.fancy * {
  position: absolute;
  color: white;
  opacity: 0.1;
}

.fancy *:nth-child(1) {
  top: 20px;
  left: 20px;
  rotate: 320deg;
}
.fancy *:nth-child(2) {
  left: 20px;
  bottom: 50px;
  rotate: 60deg;
  font-size: 2.5rem;
}

.fancy *:nth-child(3) {
  right: 40px;
  top: 150px;
  rotate: 210deg;
  font-size: 4.5rem;
}

.fancy *:nth-child(4) {
  left: 120px;
  bottom: 120px;
  rotate: 20deg;
  font-size: 1.5rem;
}

.fancy *:nth-child(5) {
  right: 220px;
  bottom: 170px;
  rotate: 50deg;
  font-size: 2rem;
}

::-webkit-scrollbar {
  width: 2px;
}

/* Track */
::-webkit-scrollbar-track {
  background: var(--dark);
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: var(--primary-color);
  border-radius: 10px;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #555;
}

@media only screen and (max-width: 992px) {
  .musics {
    border: none;
  }
}
</style>
