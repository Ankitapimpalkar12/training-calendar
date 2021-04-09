<template>
  <q-card>
    <q-card-section>
      <div class="timeline">
        <div class="slot" v-for="slot in slots" :key="slot">
          {{ slot }}
        </div>
      </div>
      <div
        class="event bg-green-3 rounded-borders"
        v-for="(event, idx) in events"
        :key="event.id"
        :style="getStyles(event, idx)"
        @click="showEvent(event)"
      >
        <span class="text-caption text-weight-bold">{{ event.subject }}</span>
        <br />
        <span class="text-caption " v-if="event.description">
          {{ event.description }}
        </span>
        <br />
        <span class="text-caption" v-if="event.location">
          Location: {{ event.location }}
        </span>
      </div>
    </q-card-section>

    <q-dialog position="right" v-model="eventDetails">
      <q-card>
        <q-card-section>
          <div class="text-h6">
            {{ selectedEvent.subject }}

            <q-btn
              class="float-right"
              round
              dense
              flat
              icon="close"
              v-close-popup
            >
            </q-btn>
          </div>
        </q-card-section>

        <q-separator></q-separator>
        <q-card-section>
          <pre>{{ selectedEvent }}</pre>
        </q-card-section>
      </q-card>
    </q-dialog>
  </q-card>
</template>

<script>
const SEGMENT_HEIGHT = 20;
const SEGMENT_WIDTH = 150;
const OFFSET = 75;

export default {
  props: ["events"],

  data() {
    return {
      eventDetails: false,

      selectedEvent: {
        subject: null
      },

      slots: [
        "00:00",
        "00:15",
        "00:30",
        "00:45",

        "01:00",
        "01:15",
        "01:30",
        "01:45",

        "02:00",
        "02:15",
        "02:30",
        "02:45",

        "03:00",
        "03:15",
        "03:30",
        "03:45",

        "04:00",
        "04:15",
        "04:30",
        "04:45",

        "05:00",
        "05:15",
        "05:30",
        "05:45",

        "06:00",
        "06:15",
        "06:30",
        "06:45"
      ]
    };
  },

  methods: {
    showEvent(event) {
      this.selectedEvent = event;

      this.eventDetails = true;
    },

    getStyles(event, idx) {
      let style = {
        height: (event.endSlot - event.startSlot) * SEGMENT_HEIGHT + "px",

        width: SEGMENT_WIDTH + "px",

        top: (event.startSlot + 1) * SEGMENT_HEIGHT + "px",

        left: OFFSET + idx * (SEGMENT_WIDTH + 10) + "px"
      };

      return style;
    }
  }
};
</script>

<style scoped lang="scss">
.event {
  position: absolute;
  padding: 4px;
  overflow-y: scroll;

  -ms-overflow-style: none;
  scrollbar-width: none; /* Firefox */

  &::-webkit-scrollbar {
    display: none;
  }

  &:hover {
    background: #43c743 !important;
  }
}

.timeline {
  margin: 4px;
}

.slot {
  border-bottom: 1px solid rgb(219, 219, 219);
  box-sizing: border-box;
  height: 20px;
  /* width: 70px; */
}
</style>
