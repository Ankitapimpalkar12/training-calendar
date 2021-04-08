<template>
  <q-page padding>
    <!-- <div class="text-h1">{{ name }}</div>
     -->

    <!-- <div class="row q-col-gutter-sm">
      <div class="col-1 bg-red"></div>
      <div class="col-1 bg-green"></div>

      <div class="col-4 bg-red"></div>
      <div class="col-1 bg-green"></div>

      <div class="col-1 bg-red"></div>
      <div class="col-1 bg-green"></div>

      <div class="col-1 bg-red"></div>
      <div class="col-1 bg-green"></div>

      <div class="col-1 bg-red"></div>
      <div class="col-1 bg-green"></div>

      <div class="col-1 bg-red"></div>
      <div class="col-1 bg-green"></div>
    </div> -->

    <div class="row q-col-gutter-md">
      <div class="col-4">
        <q-card>
          <q-card-section>
            <div class="text-h6">Create New Event</div>
          </q-card-section>
          <q-separator />

          <q-card-section>
            <q-input
              outlined
              dense
              placeholder="scheduleEndDate"
              v-model="newEvent.scheduleEndDate"
            >
              <template v-slot:prepend>
                <q-icon name="schedule"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="scheduleDate"
              v-model="newEvent.scheduleDate"
            >
              <template v-slot:prepend>
                <q-icon name="schedule"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="scheduleStartDate"
              v-model="newEvent.scheduleStartDate"
            >
              <template v-slot:prepend>
                <q-icon name="book"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="reservationId"
              v-model="newEvent.reservationId"
            >
              <template v-slot:prepend>
                <q-icon name="accessibility_new"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="orgId"
              v-model="newEvent.orgId"
            >
              <template v-slot:prepend>
                <q-icon name="work_outline"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="startSlot"
              v-model="newEvent.startSlot"
            >
              <template v-slot:prepend>
                <q-icon name="touch_app"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="endSlot"
              v-model="newEvent.endSlot"
            >
              <template v-slot:prepend>
                <q-icon name="touch_app"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="employeeIds"
              v-model="newEvent.employeeIds"
            >
              <template v-slot:prepend>
                <q-icon name="work.outline"></q-icon>
              </template>
            </q-input>
            <q-input
              outlined
              dense
              placeholder="taskId"
              v-model="newEvent.taskId"
            >
              <template v-slot:prepend>
                <q-icon name="task"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="audioRequired"
              v-model="newEvent.audioRequired"
            >
              <template v-slot:prepend>
                <q-icon name="record_voice_over"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="subject"
              v-model="newEvent.subject"
            >
              <template v-slot:prepend>
                <q-icon name="subject"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="description"
              v-model="newEvent.description"
            >
              <template v-slot:prepend>
                <q-icon name="description"></q-icon>
              </template>
            </q-input>

            <q-input
              outlined
              dense
              placeholder="location"
              v-model="newEvent.location"
            >
              <template v-slot:prepend>
                <q-icon name="location_on"></q-icon>
              </template>
            </q-input>

            <q-btn label="Save" @click="saveEvent" color="primary" />
            <q-btn label="Fetch" @click="fetchEvents" color="primary" />
          </q-card-section>
        </q-card>
      </div>
      <div class="col-8">
        <q-calendar
          v-model="selectedDate"
          view="week"
          locale="en-us"
          :interval-minutes="60"
          style="height: 800px;"
        >
          <template #day-header="{ timestamp }">
            <div class="row justify-center">
              <template v-for="(event, index) in eventsMap[timestamp.date]">
                <q-badge
                  v-if="!event.time"
                  :key="index"
                  style="width: 100%; cursor: pointer;"
                  :class="badgeClasses(event, 'header')"
                  :style="badgeStyles(event, 'header')"
                >
                  <q-icon
                    v-if="event.icon"
                    :name="event.icon"
                    class="q-mr-xs"
                  ></q-icon
                  ><span class="ellipsis">{{ event.title }}</span>
                </q-badge>
                <q-badge
                  v-else
                  :key="index"
                  class="q-ma-xs"
                  :class="badgeClasses(event, 'header')"
                  :style="badgeStyles(event, 'header')"
                  style="width: 10px; max-width: 10px; height: 10px; max-height: 10px"
                />
              </template>
            </div>
          </template>

          <template #day-body="{timestamp, timeStartPos, timeDurationHeight }">
            <template v-for="(event, index) in getEvents(timestamp.date)">
             
              <q-badge
                v-if="event"
                :key="index"
                class="my-event justify-center ellipsis text-white bg-green full-width"
                :style="
                  badgeStyles(event, 'body', timeStartPos, timeDurationHeight)
                "
              >
                <q-icon
                  v-if="event.icon"
                  :name="event.icon"
                  class="q-mr-xs"
                ></q-icon
                ><span class="ellipsis">{{ event.subject }}</span>
              </q-badge> 
            </template>
          </template>
        </q-calendar>
      </div>
    </div>
  </q-page>
</template>

<script>
// normally you would not import "all" of QCalendar, but is needed for this example to work with UMD (codepen)
import QCalendar from "@quasar/quasar-ui-qcalendar"; // ui is aliased from '@quasar/quasar-ui-qcalendar'

const CURRENT_DAY = new Date();

function getCurrentDay(day) {
  const newDay = new Date(CURRENT_DAY);
  newDay.setDate(day);
  const tm = QCalendar.parseDate(newDay);
  return tm.date;
}

const reRGBA = /^\s*rgb(a)?\s*\((\s*(\d+)\s*,\s*?){2}(\d+)\s*,?\s*([01]?\.?\d*?)?\s*\)\s*$/;

function textToRgb(color) {
  if (typeof color !== "string") {
    throw new TypeError("Expected a string");
  }

  const m = reRGBA.exec(color);
  if (m) {
    const rgb = {
      r: Math.min(255, parseInt(m[2], 10)),
      g: Math.min(255, parseInt(m[3], 10)),
      b: Math.min(255, parseInt(m[4], 10))
    };
    if (m[1]) {
      rgb.a = Math.min(1, parseFloat(m[5]));
    }
    return rgb;
  }
  return hexToRgb(color);
}

function hexToRgb(hex) {
  if (typeof hex !== "string") {
    throw new TypeError("Expected a string");
  }

  hex = hex.replace(/^#/, "");

  if (hex.length === 3) {
    hex = hex[0] + hex[0] + hex[1] + hex[1] + hex[2] + hex[2];
  } else if (hex.length === 4) {
    hex = hex[0] + hex[0] + hex[1] + hex[1] + hex[2] + hex[2] + hex[3] + hex[3];
  }

  const num = parseInt(hex, 16);

  return hex.length > 6
    ? {
        r: (num >> 24) & 255,
        g: (num >> 16) & 255,
        b: (num >> 8) & 255,
        a: Math.round((num & 255) / 2.55)
      }
    : { r: num >> 16, g: (num >> 8) & 255, b: num & 255 };
}

function luminosity(color) {
  if (typeof color !== "string" && (!color || color.r === undefined)) {
    throw new TypeError("Expected a string or a {r, g, b} object as color");
  }

  const rgb = typeof color === "string" ? textToRgb(color) : color,
    r = rgb.r / 255,
    g = rgb.g / 255,
    b = rgb.b / 255,
    R = r <= 0.03928 ? r / 12.92 : Math.pow((r + 0.055) / 1.055, 2.4),
    G = g <= 0.03928 ? g / 12.92 : Math.pow((g + 0.055) / 1.055, 2.4),
    B = b <= 0.03928 ? b / 12.92 : Math.pow((b + 0.055) / 1.055, 2.4);
  return 0.2126 * R + 0.7152 * G + 0.0722 * B;
}

const API_KEY =
  "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYW5vbiIsImlhdCI6MTYxNzYxNzIxMSwiZXhwIjoxOTMzMTkzMjExfQ.rghEFUJ6cBrv-WVZdgwuK2dhjbnHlnGGytnY8HwbCCM";

export default {
  name: "PageIndex",

  data() {
    return {
      selectedDate: null,

      events: [],

      name: "Ankita's Calendar",

      newEvent: {
        scheduleEndDate: null,
        // scheduleDate: null,
        scheduleStartDate: null,
        reservationId: null,
        orgId: null,
        startSlot: null,
        endSlot: null,
        employeeIds: null,
        taskId: null,
        audioRequired: null,
        subject: null,
        description: null,
        location: null
      }
    };
  },

  computed: {
    // convert the events into a map of lists keyed by date
    eventsMap() {
      const map = {};
      this.events.forEach(event =>
        (map[event.scheduleStartDate] =
          map[event.scheduleStartDate] || []).push(event)
      );
      return map;
    }
  },

  methods: {
    isCssColor(color) {
      return !!color && !!color.match(/^(#|(rgb|hsl)a?\()/);
    },

    badgeClasses(event, type) {
      const cssColor = this.isCssColor(event.bgcolor);
      const isHeader = type === "header";
      return {
        [`text-white bg-${event.bgcolor}`]: !cssColor,
        "full-width": !isHeader && (!event.side || event.side === "full"),
        "left-side": !isHeader && event.side === "left",
        "right-side": !isHeader && event.side === "right"
      };
    },

    saveEvent() {
      this.$q.loading.show();

      this.$axios
        .post(
          "https://qfucohkyppxdwghixbot.supabase.co/rest/v1/Events",
          this.newEvent,
          {
            headers: {
              Authorization: "Bearer " + API_KEY,
              apiKey: API_KEY
            }
          }
        )
        .then(response => {
          console.log(response.data);
        })
        .catch(err => {
          console.error(err);
        })
        .finally(() => {
          this.$q.loading.hide();
        });
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
    },

    badgeStyles(event, type, timeStartPos, timeDurationHeight) {
      const s = {};
      if (this.isCssColor(event.bgcolor)) {
        s["background-color"] = event.bgcolor;
        s.color = luminosity(event.bgcolor) > 0.5 ? "black" : "white";
      }
      if (timeStartPos) {
        console.log('startPos',timeStartPos(event.time))
        s.top = timeStartPos(event.time) + "px";
      }
      if (timeDurationHeight) {
        console.log('height',timeDurationHeight(event.duration) )
        s.height = timeDurationHeight(event.duration) + "px";
      }
      s["align-items"] = "flex-start";
      return s;
    },

    getEvents(dt) {
      const currentDate = QCalendar.parsed(dt);
      const events = [];
      for (let i = 0; i < this.events.length; ++i) {
        let added = false;
        const event = this.events[i];
        if (event.scheduleStartDate === dt) {
          if (event.time) {
            if (events.length > 0) {
              // check for overlapping times

              const startTime = QCalendar.parsed(
                event.scheduleStartDate + " " + event.time
              );
              const endTime = QCalendar.addToDate(startTime, {
                minute: event.duration
              });
              for (let j = 0; j < events.length; ++j) {
                if (events[j].time) {
                  const startTime2 = QCalendar.parsed(
                    events[j].date + " " + events[j].time
                  );
                  const endTime2 = QCalendar.addToDate(startTime2, {
                    minute: events[j].duration
                  });
                  if (
                    QCalendar.isBetweenDates(startTime, startTime2, endTime2) ||
                    QCalendar.isBetweenDates(endTime, startTime2, endTime2)
                  ) {
                    events[j].side = "left";
                    event.side = "right";
                    events.push(event);
                    added = true;
                    break;
                  }
                }
              }
            }
          }
          if (!added) {
            event.side = undefined;
            events.push(event);
          }
        } else if (event.days) {
          // check for overlapping dates
          const startDate = QCalendar.parsed(event.scheduleStartDate);
          const endDate = QCalendar.addToDate(startDate, {
            day: event.days || 1
          });
          if (QCalendar.isBetweenDates(currentDate, startDate, endDate)) {
            events.push(event);
            added = true;
          }
        }
      }
      return events;
    }
  }
};
</script>
