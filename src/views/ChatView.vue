<template>
  <v-container fill-height>
    <v-row align="center" justify="center">
      <v-col v-show="showRemote" cols="12" sm="12" md="6">
        <v-card flat dense class="teal darken-4 px-2 pt-2">
          <video
            autoplay
            playsinline
            ref="remoteVideo"
            class="remoteVideo"
          ></video>
        </v-card>
      </v-col>
      <v-col cols="12" sm="12" md="6">
        <v-card flat dense class="teal darken-4 px-2 pt-2">
          <video
            id="myStream"
            muted="muted"
            ref="localVideo"
            :srcObject="localStreamSource"
            autoplay
            playsinline
          ></video>
        </v-card>
      </v-col>
    </v-row>
    <v-footer app clipped-left class="teal darken-3 py-4 elevation-2">
      <v-row align="center" justify="center">
        <v-col class="text-center" cols="12">
          <div class="control-btns ml-md-3">
            <v-btn @click="toggleAudio" depressed small class="fab">
              <svg
                v-if="!audio"
                width="16"
                height="16"
                fill="currentColor"
                class="text--red"
                viewBox="0 0 16 16"
              >
                <path d="M5 3a3 3 0 0 1 6 0v5a3 3 0 0 1-6 0V3z" />
                <path
                  d="M3.5 6.5A.5.5 0 0 1 4 7v1a4 4 0 0 0 8 0V7a.5.5 0 0 1 1 0v1a5 5 0 0 1-4.5 4.975V15h3a.5.5 0 0 1 0 1h-7a.5.5 0 0 1 0-1h3v-2.025A5 5 0 0 1 3 8V7a.5.5 0 0 1 .5-.5z"
                />
              </svg>
              <svg
                v-else-if="audio"
                xmlns="http://www.w3.org/2000/svg"
                width="16"
                height="16"
                fill="currentColor"
                class="green--text"
                viewBox="0 0 16 16"
              >
                <path
                  d="M13 8c0 .564-.094 1.107-.266 1.613l-.814-.814A4.02 4.02 0 0 0 12 8V7a.5.5 0 0 1 1 0v1zm-5 4c.818 0 1.578-.245 2.212-.667l.718.719a4.973 4.973 0 0 1-2.43.923V15h3a.5.5 0 0 1 0 1h-7a.5.5 0 0 1 0-1h3v-2.025A5 5 0 0 1 3 8V7a.5.5 0 0 1 1 0v1a4 4 0 0 0 4 4zm3-9v4.879L5.158 2.037A3.001 3.001 0 0 1 11 3z"
                />
                <path
                  d="M9.486 10.607 5 6.12V8a3 3 0 0 0 4.486 2.607zm-7.84-9.253 12 12 .708-.708-12-12-.708.708z"
                />
              </svg>
            </v-btn>

            <v-btn @click="toggleVideo" depressed small class="fab ml-1">
              <svg
                v-if="!video"
                version="1.1"
                viewBox="0 0 129 129"
                width="16"
                height="16"
                enable-background="new 0 0 129 129"
                class="hover:text-red-500"
              >
                <g>
                  <path
                    d="m96.6,26.8h-86.1c-2.2,0-4.1,1.8-4.1,4.1v67.2c0,2.2 1.8,4.1 4.1,4.1h86.1c2.2,0 4.1-1.8 4.1-4.1v-19.4l14.9,14.9c0.8,0.8 1.8,1.2 2.9,1.2 0.5,0 1.1-0.1 1.6-0.3 1.5-0.6 2.5-2.1 2.5-3.8v-52.5c0-1.6-1-3.1-2.5-3.8-1.5-0.6-3.3-0.3-4.4,0.9l-14.9,14.9v-19.3c-0.1-2.3-1.9-4.1-4.2-4.1zm-4.1,33.3v8.8 25.2h-78v-59.2h78v25.2zm21.9-12v32.9l-13.7-13.7v-5.4l13.7-13.8z"
                  />
                </g>
              </svg>

              <svg
                v-else-if="video"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                width="16"
                height="16"
                class="hover:text-red-500"
              >
                <path
                  d="M16 16v1a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V7a2 2 0 0 1 2-2h2m5.66 0H14a2 2 0 0 1 2 2v3.34l1 1L23 7v10"
                />
                <line x1="1" y1="1" x2="23" y2="23" />
              </svg>
            </v-btn>
            <v-btn
              v-show="showRemote"
              @click="dropCall"
              depressed
              small
              class="fab ml-1"
            >
              <v-icon color="red darken-2">mdi-phone-cancel</v-icon>
            </v-btn>
          </div>
        </v-col>
      </v-row>
    </v-footer>
    <v-dialog v-model="incoming" persistent max-width="290" class="elevation-2">
      <v-card>
        <v-card-title class="ml-0 border-0 text-h4">
          <v-spacer></v-spacer
          ><span class="green--text darken-6 text-h6">INCOMING CALL</span
          ><v-spacer></v-spacer>
        </v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn medium fab color="green darken-2" @click="acceptCall">
            <v-icon class="white--text">mdi-phone</v-icon>
          </v-btn>
          <v-spacer></v-spacer>
          <v-btn color="red darken-2" fab @click="dropCall" medium>
            <v-icon class="white--text">mdi-phone-cancel</v-icon>
          </v-btn>
          <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>
<script>
export default {
  data: () => {
    return {
      myVideo: null,
    };
  },
  computed: {
    showRemote() {
      return this.$store.state.oncall;
    },
    streamSource() {
      return this.$store.getters.remoteStream;
    },
    localStreamSource() {
      return this.$store.getters.localStream;
    },
    audio() {
      return this.$store.getters.enableAudio;
    },
    video() {
      return this.$store.getters.enableVideo;
    },
    incoming() {
      let callState = this.$store.getters.incoming;
      this.oncall = callState;
      return callState;
    },
    callEnded() {
      return this.$store.state.callEnded;
    },
  },
  methods: {
    acceptCall() {
      this.$store.commit("answer");
    },
    dropCall() {
      this.$store.commit("decline");
      this.$store.commit("resetCallState");
      //this.createSocket();
      //this.createPeerObject();
    },
    mountStream() {
      this.$refs.remoteVideo.srcObject = this.streamSource;
    },
    getUserMedia() {
      this.$store.dispatch("getMedia");
    },
    mountLocalStream() {
      this.myVideo.srcObject = this.localStreamSource;
    },
    toggleAudio() {
      this.$store.commit("toggleAudio");
    },
    toggleVideo() {
      this.$store.commit("toggleVideo");
    },
    createSocket() {
      this.$store.dispatch("createSignalChannel")
    }
  },
  created() {
    this.createSocket();
  },
  watch: {
    localStreamSource(oldval, newval) {
      this.mountLocalStream();
    },
    streamSource(newVal, oldVal) {
      this.mountStream();
    },
    callEnded(newval, oldval) {
      if (newval === true) {
        this.$store.commit("resetCallState");
      }
    },
  },
  mounted() {
    this.myVideo = this.$refs.localVideo;
    this.getUserMedia();
  },
};
</script>

<style scoped>
#myStream {
  width: 100% !important;
  height: auto !important;
}
.video-container {
  width: 100vw;
  max-width: 1000px;
  display: flex;
  justify-content: center;
  masrgin-inline: auto;
}
.remoteVideo {
  /* override other styles to make responsive */
  width: 100% !important;
  height: auto !important;
}
</style>
