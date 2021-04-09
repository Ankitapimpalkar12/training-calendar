<template>
  <q-card style="width: 450px">
    <q-card-section>
      <div class="text-h6">
        Create New Event

        <q-btn class="float-right" round dense flat icon="close" v-close-popup>
        </q-btn>
      </div>
    </q-card-section>
    <q-separator />

    <q-card-section>
      <div class="row q-col-gutter-sm">
        <div class="col-6">
          <q-input
            outlined
            dense
            placeholder="scheduleStartDate"
            v-model="newEvent.scheduleStartDate"
          >
            <template v-slot:prepend>
              <q-icon name="book"></q-icon>
            </template>

            <template v-slot:append>
              <q-icon name="event" class="cursor-pointer">
                <q-popup-proxy
                  ref="startDate"
                  transition-show="scale"
                  transition-hide="scale"
                >
                  <q-date
                    minimal
                    v-model="newEvent.scheduleStartDate"
                    mask="YYYY-MM-DD"
                    @input="$refs.startDate.hide()"
                  >
                  </q-date>
                </q-popup-proxy>
              </q-icon>
            </template>
          </q-input>
        </div>

        <div class="col-6">
          <q-input
            outlined
            dense
            placeholder="scheduleEndDate"
            v-model="newEvent.scheduleEndDate"
          >
            <template v-slot:prepend>
              <q-icon name="schedule"></q-icon>
            </template>
            <template v-slot:append>
              <q-icon name="event" class="cursor-pointer">
                <q-popup-proxy
                  ref="endDate"
                  transition-show="scale"
                  transition-hide="scale"
                >
                  <q-date
                    minimal
                    v-model="newEvent.scheduleEndDate"
                    mask="YYYY-MM-DD"
                    @input="$refs.endDate.hide()"
                  >
                  </q-date>
                </q-popup-proxy>
              </q-icon>
            </template>
          </q-input>
        </div>

        <div class="col-12">
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
        </div>

        <div class="col-6">
          <q-select
            outlined
            dense
            placeholder="startSlot"
            v-model="newEvent.startSlot"
            :options="slots"
            map-options
            emit-value
          >
            <template v-slot:prepend>
              <q-icon name="touch_app"></q-icon>
            </template>
          </q-select>
        </div>

        <div class="col-6">
          <q-select
            outlined
            dense
            placeholder="endSlot"
            v-model="newEvent.endSlot"
            :options="slots"
            map-options
            emit-value
          >
            <template v-slot:prepend>
              <q-icon name="touch_app"></q-icon>
            </template>
          </q-select>
        </div>

        <div class="col-4">
          <q-input outlined dense placeholder="orgId" v-model="newEvent.orgId">
            <template v-slot:prepend>
              <q-icon name="work_outline"></q-icon>
            </template>
          </q-input>
        </div>

        <div class="col-4">
          <q-input
            outlined
            dense
            placeholder="employeeIds"
            v-model="newEvent.employeeIds"
          >
            <template v-slot:prepend>
              <q-icon name="person"></q-icon>
            </template>
          </q-input>
        </div>
        <div class="col-4">
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
        </div>

        <div class="col-12">
          <q-item tag="label" v-ripple dense>
            <q-item-section avatar>
              <q-icon name="record_voice_over"></q-icon>
            </q-item-section>
            <q-item-section>
              <q-item-label>audioRequired</q-item-label>
            </q-item-section>

            <q-item-section side>
              <q-checkbox
                v-model="newEvent.audioRequired"
                val="teal"
                color="teal"
              />
            </q-item-section>
          </q-item>
        </div>

        <div class="col-12">
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
        </div>

        <div class="col-12">
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
        </div>

        <div class="col-12">
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
        </div>

        <div class="col-12 text-right">
          <q-btn label="Save" @click="saveEvent" color="primary" />
        </div>
      </div>
    </q-card-section>
  </q-card>
</template>

<script>
import moment from "moment";
const API_KEY =
  "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYW5vbiIsImlhdCI6MTYxNzYxNzIxMSwiZXhwIjoxOTMzMTkzMjExfQ.rghEFUJ6cBrv-WVZdgwuK2dhjbnHlnGGytnY8HwbCCM";

export default {
  data() {
    return {
      newEvent: {
        scheduleEndDate: null,
        scheduleDate: new moment().format("YYYY/MM/DD"),
        scheduleStartDate: null,
        reservationId: null,
        orgId: null,
        startSlot: 0,
        endSlot: 0,
        employeeIds: null,
        taskId: null,
        audioRequired: false,
        subject: null,
        description: null,
        location: null
      },

      slots: [
        { value: 0, label: "00:00" },
        { value: 1, label: "00:15" },
        { value: 2, label: "00:30" },
        { value: 3, label: "00:45" },

        { value: 4, label: "01:00" },
        { value: 5, label: "01:15" },
        { value: 6, label: "01:30" },
        { value: 7, label: "01:45" },

        { value: 8, label: "02:00" },
        { value: 9, label: "02:15" },
        { value: 10, label: "02:30" },
        { value: 11, label: "02:45" },

        { value: 12, label: "03:00" },
        { value: 13, label: "03:15" },
        { value: 14, label: "03:30" },
        { value: 15, label: "03:45" },

        { value: 16, label: "04:00" },
        { value: 17, label: "04:15" },
        { value: 18, label: "04:30" },
        { value: 19, label: "04:45" },

        { value: 20, label: "05:00" },
        { value: 21, label: "05:15" },
        { value: 22, label: "05:30" },
        { value: 23, label: "05:45" },

        { value: 24, label: "06:00" },
        { value: 25, label: "06:15" },
        { value: 26, label: "06:30" },
        { value: 27, label: "06:45" }
      ]
    };
  },

  methods: {
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
          this.$emit("done");
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
