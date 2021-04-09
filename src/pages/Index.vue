<template>
  <q-page padding>
    <div class="row q-col-gutter-md">
      <div class="col-4">
        <div class="row q-col-gutter-md">
          <div class="col-12">
            <q-btn
              @click="showForm"
              class="full-width"
              label="Add Event"
              color="blue"
            ></q-btn>
          </div>
          <div class="col-12">
            <q-date class="full-width" v-model="selectedDate"></q-date>
          </div>
        </div>
      </div>
      <div class="col-8">
        <Timeline :events="filteredEvents" />
      </div>
    </div>

    <q-dialog v-model="showNewEventForm" position="left">
      <EventForm @done="handleFormDone" />
    </q-dialog>
  </q-page>
</template>

<script>
import EventForm from "components/EventForm";
import Timeline from "components/Timeline";
import moment from "moment-timezone";

const CURRENT_DAY = new moment().tz("Asia/Kolkata");

const API_KEY =
  "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYW5vbiIsImlhdCI6MTYxNzYxNzIxMSwiZXhwIjoxOTMzMTkzMjExfQ.rghEFUJ6cBrv-WVZdgwuK2dhjbnHlnGGytnY8HwbCCM";

export default {
  name: "PageIndex",

  components: {
    EventForm,
    Timeline
  },

  computed: {
    filteredEvents() {
      let selectedDate = moment(this.selectedDate).format("YYYY-MM-DD");
      return this.events
        .filter(event => event.scheduleStartDate == selectedDate)
        .sort((e1, e2) => e1.startSlot - e2.startSlot);
    }
  },

  data() {
    return {
      selectedDate: CURRENT_DAY.format("YYYY/MM/DD"),

      showNewEventForm: false,

      events: [],

      name: "Ankita's Calendar"
    };
  },

  mounted() {
    this.fetchEvents();
  },

  methods: {
    showForm() {
      this.showNewEventForm = true;
    },

    handleFormDone() {
      this.showNewEventForm = false;
      this.fetchEvents();
    },

    fetchEvents() {
      this.$q.loading.show();

      this.$axios
        .get(
          "https://qfucohkyppxdwghixbot.supabase.co/rest/v1/Events",

          {
            headers: {
              Authorization: "Bearer " + API_KEY,
              apiKey: API_KEY
            }
          }
        )
        .then(response => {
          this.events = response.data;
          this.events.forEach(event => {
            let starTimeHours = Math.floor(event.startSlot / 4); // 86 = 21.5 hours = 84/4
            let startTimeMinutes = 15 * (event.startSlot % 4); // 86 % 4 = 2 , 2*15 = 30

            event.time = starTimeHours < 10 ? "0" : "";
            event.time += starTimeHours + ":";
            event.time += startTimeMinutes < 10 ? "0" : "";
            event.time += startTimeMinutes; // hh:mm

            event.duration = (event.endSlot - event.startSlot) * 15; // in minutes
          });
        })
        .catch(err => {
          console.error(err);
        })
        .finally(() => {
          this.$q.loading.hide();
        });
    }
  }
};
</script>
