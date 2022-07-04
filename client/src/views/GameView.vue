<template>
  <div class="is-fixed-top message-bar">
    <p>{{ message }}</p>
  </div>
  <section class="section">
    <splitpanes class="default-theme">
      <pane :size="65" v-if="!amICurrentCzar">
        <player-view
          :currentPlayer="currentPlayer"
          :playerMessage="playerMessage"
          :isMobile="isMobile"
        />
      </pane>

      <pane v-if="!isMobile || amICurrentCzar" :size="35">
        <div class="czar-container">
          <status />
          <czar-view
            :playerSelections="playerSelections"
            :currentBlackCard="currentBlackCard"
            :amICurrentCzar="amICurrentCzar"
            :czarMessage="czarMessage"
            :roomId="currentPlayer.roomId"
          />
        </div>
      </pane>
    </splitpanes>
  </section>
</template>

<script>
import "splitpanes/dist/splitpanes.css";
import { defineComponent } from "vue";
import { Splitpanes, Pane } from "splitpanes";
import CzarView from "./CzarView.vue";
import PlayerView from "./PlayerView.vue";
import Status from "./Status.vue";

export default defineComponent({
  name: "PlayView",
  components: {
    CzarView,
    CzarView,
    Pane,
    PlayerView,
    Splitpanes,
    Status,
  },
  props: {
    currentPlayer: Object,
  },
  data() {
    return {
      isMobile:
        /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(
          navigator.userAgent
        ),
      amICurrentCzar: false,
      currentCzar: "",
      currentBlackCard: "",
      message: "",
      playerMessage: "Please choose a white card to fill in the blank",
      czarMessage:
        "Please wait for the other players to select their white card",
      playerSelections: [],
    };
  },
  methods: {},
  sockets: {
    update_playground(data) {
      this.currentCzar = data.currentCzar;
      this.currentBlackCard = data.currentBlackCard;
      this.amICurrentCzar = this.currentPlayer.name === data.currentCzar.name;
      this.message = `${data.publicMessage}`;
    },
    czar_chooses(data) {
      this.playerSelections = data.playerSelections;
      this.message = `All players have selected a white card. Czar ${this.currentCzar.name} will now choose his favorite!`;
      this.playerMessage = `Waiting on Czar ${this.currentCzar.name}`;
      this.czarMessage = "Please select your favorite answer!";
    },
  },
});
</script>

<style lang="scss" scoped>
.section {
  padding: 32px 0 0 0;
  height: 100%;
}

.message-bar {
  width: 100%;
  background-color: black;
  color: white;
  font-size: 15px;
  padding: 5px;
  z-index: 10000;
  position: fixed;
}

.czar-container {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  padding: 10px;
  height: 100%;
}
</style>