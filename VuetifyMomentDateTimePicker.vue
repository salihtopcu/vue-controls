<template>
  <v-dialog ref="dialog" v-model="modal" :return-value.sync="date" persistent width="290px">
    <template v-slot:activator="{ on }">
      <!-- v-bind:value="value" -->
      <v-text-field
        v-bind:value="formattedDateTime"
        :label="label"
        prepend-icon="event"
        readonly
        v-bind:disabled="disabled"
        v-on="on"
        :clearable="clearable"
        @click:clear="handleClear()"
      ></v-text-field>
    </template>

    <v-date-picker
      v-model="date"
      v-if="mode === 'date'"
      :scrollable="datePicker.scrollable"
      @change="handleDateChange(date)"
    >
      <div class="flex-grow-1"></div>
      <v-btn text color="primary" @click="handleDateCancel">Cancel</v-btn>
      <v-btn text color="primary" @click="handleDateOk">OK</v-btn>
    </v-date-picker>

    <v-time-picker
      v-model="time"
      v-if="mode === 'time'"
      :format="timePicker.format"
      :scrollable="timePicker.scrollable"
      @change="handleTimeChange(time)"
    >
      <div class="flex-grow-1"></div>
      <v-btn text color="primary" @click="handleTimeCancel">Cancel</v-btn>
      <v-btn text color="primary" @click="handleTimeOk">OK</v-btn>
    </v-time-picker>
  </v-dialog>
</template>

<script>
import moment from "moment";

export default {
  props: ["value", "clearable", "label", "disabled", "dateFormat"],
  data() {
    return {
      modalVisibality: false,
      mode: "date", // date | time
      date: "",
      time: "",
      dateTime: null,
      formattedDateTime: "",
      datePicker: {
        scrollable: true,
        displayFormat: "YYYY-MM-DD"
      },
      timePicker: {
        format: "24hr", // 24hr | ampm
        scrollable: true,
        displayFormat: "HH:mm"
      }
    };
  },
  watch: {
    value: function(newValue, oldValue) {
      if (newValue !== oldValue) this.setDateAndTimeByCurrentValue();
    },
    dateTime: function(newValue, oldValue) {
      this.formattedDateTime = moment.isMoment(this.dateTime)
        ? this.dateTime.format(
            `${this.datePicker.displayFormat} ${this.timePicker.displayFormat}`
          )
        : "";
    }
  },
  computed: {
    modal: {
      get: function() {
        return this.modalVisibality;
      },
      set: function(newValue) {
        this.modalVisibality = newValue;
        if (newValue) this.setDateAndTimeByCurrentValue();
        else this.mode = "date";
      }
    }
  },
  created() {
    if (this.dateFormat) this.datePicker.displayFormat = this.dateFormat;
    this.setDateAndTimeByCurrentValue();
  },
  methods: {
    setDateAndTimeByCurrentValue() {
      if (moment.isMoment(this.value)) {
        this.date = this.value.format("YYYY-MM-DD");
        this.time = this.value.format("HH:mm:SS");
      } else {
        this.date = "";
        this.time = "";
      }
      this.setDateTimeByCurrentDateAndTime();
    },
    setDateTimeByCurrentDateAndTime() {
      if (this.time === "") this.time = "00:00:00";
      this.dateTime =
        this.date !== "" ? moment(`${this.date}T${this.time}`.trim()) : null;
    },
    handleClear() {
      this.$emit("input", this.dateTime);
    },
    handleDateChange(newDate) {
      this.date = newDate;
    },
    handleDateOk() {
      this.setDateTimeByCurrentDateAndTime();
      this.$emit("input", this.dateTime);
      this.mode = "time";
    },
    handleDateCancel() {
      this.modal = false;
    },
    handleTimeChange(newTime) {
      this.time = `${newTime}:00`;
    },
    handleTimeOk() {
      this.setDateTimeByCurrentDateAndTime();
      this.$emit("input", this.dateTime);
      this.modal = false;
    },
    handleTimeCancel() {
      this.modal = false;
    }
  }
};
</script>
