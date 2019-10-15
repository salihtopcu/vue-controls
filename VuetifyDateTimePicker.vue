<template>
  <v-dialog ref="dialog" v-model="modal" :return-value.sync="date" persistent width="290px">
    <template v-slot:activator="{ on }">
      <v-text-field
        v-bind:value="value"
        :label="label"
        prepend-icon="event"
        readonly
        v-on="on"
        :clearable="clearable"
        @click:clear="handleClear()"
      ></v-text-field>
    </template>

    <v-date-picker
      v-model="date"
      v-if="mode == 'date'"
      :scrollable="datePicker.scrollable"
      @change="handleDateChange(date)"
    >
      <div class="flex-grow-1"></div>
      <v-btn text color="primary" @click="handleDateCancel">Cancel</v-btn>
      <v-btn text color="primary" @click="handleDateOk">OK</v-btn>
    </v-date-picker>
    <v-time-picker
      v-model="time"
      v-if="mode == 'time'"
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
export default {
  props: ["value", "clearable", "label"],
  watch: {
    value: function(newValue, oldValue) {
      if (newValue) this.dateTime = newValue;
    }
  },
  data() {
    return {
      modalVisibality: false,
      mode: "date", // date | time
      date: "",
      time: "",
      datePicker: {
        scrollable: true
      },
      timePicker: {
        format: "24hr", // 24hr | ampm
        scrollable: true
      }
    };
  },
  computed: {
    modal: {
      get: function() {
        return this.modalVisibality;
      },
      set: function(newValue) {
        this.modalVisibality = newValue;
        if (this.value && this.value !== "") this.dateTime = this.value;
        if (!newValue) this.mode = "date";
      }
    },
    dateTime: {
      get: function() {
        if (this.time === "") {
          this.time = this.date === "" ? "" : "00:00";
        }
        return (this.date + " " + this.time).trim();
      },
      set: function(newValue) {
        const values = newValue.split(" ");
        if (values.length == 2) {
          this.date = values[0];
          this.time = values[1];
        } else {
          this.date = "";
          this.time = "";
        }
      }
    }
  },
  methods: {
    handleClear() {
      this.dateTime = "";
      this.$emit("input", this.dateTime);
    },
    handleDateChange(newDate) {
      this.date = newDate;
    },
    handleDateOk() {
      this.$emit("input", this.dateTime);
      this.mode = "time";
    },
    handleDateCancel() {
      this.modal = false;
    },
    handleTimeChange(newTime) {
      this.time = newTime;
    },
    handleTimeOk() {
      this.$emit("input", this.dateTime);
      this.modal = false;
    },
    handleTimeCancel() {
      this.modal = false;
    }
  }
};
</script>
