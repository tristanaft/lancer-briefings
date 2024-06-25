<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "001",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "The Drop",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Grim",
          "alias": "Felix Kaiser",
          "code": "19539a9e-2b9b-4d68-802e-6022feebbfb7//NDL-C-DELTA-SEPTEMBER",
          "corpro": "GMS",
          "frame": "Chomolungma",
          "mech": "Uncomfortably Vague"
        },
        {
          "callsign": "N4N0",
          "alias": "Isambard Brunel",
          "code": "99ee4b67-feef-46a7-a44e-74ab1bfaabe3//NDL-C-SORROW-EYE",
          "corpro": "GMS",
          "frame": "Chomolungma",
          "mech": "BYT3"
        },
        {
          "callsign": "Rambo",
          "alias": "Stanley Barnsburger",
          "code": "a3b7fe44-93f8-4ed5-a9ec-f47761bc3c5b//NDL-C-DELTA-CROSS",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Century"
        },
        {
          "callsign": "sLAUGHTER",
          "alias": "Warai Otoko",
          "code": "516132e9-b137-42a3-93af-b58eee403fbb//NDL-C-THIRD-DOLMEN",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Ghost_0.02"
        },
        {
          "callsign": "Vicar",
          "alias": "Ehrengard Reiswig",
          "code": "4ecc0a0d-b43e-4a0c-b1b0-8e2664f9c008//NDL-C-SINGULARITY-REACH",
          "corpro": "GMS",
          "frame": "Sagarmatha",
          "mech": "IMHU11U"
        },
        {
          "callsign": "X-S",
          "alias": "Xander Slayton",
          "code": "3d11df3a-89c9-4b18-b185-bb797d028c11//NDL-C-STONE-ROYAL ",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Megatron"
        }
      ],
      "header": {
        "planet": "Cressidium",
        "year": "5013u",
        "system": "Lassen-5",
        "gate": "Cascade-Rainier",
        "ring": "Cascade-Line",
        "headerTitle": "Union Navy",
        "headerSubtitle": "Rio Grande",
        "subheaderTitle": "Diplomatic Escort",
        "subheaderSubtitle": "Sierra-Oscar-Romeo",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
