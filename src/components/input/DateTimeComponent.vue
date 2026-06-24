<template>
  <div class="date-time-component">
    <label v-if="label" class="date-time-label">{{ label }}</label>
    <div class="date-time-inputs">
      <input
        type="date"
        placeholder="yyyy-mm-dd"
        :value="dateValue"
        @input="updateDate($event.target.value)"
        class="date-input"
      />
      <input
        type="time"
        :value="timeValue"
        @input="updateTime($event.target.value)"
        class="time-input"
      />
    </div>
  </div>
</template>

<script setup>
import { computed } from "vue";
import dayjs from "dayjs";

const props = defineProps({
  modelValue: {
    type: String,
    default: "",
  },
  label: {
    type: String,
    default: "",
  },
});

const emit = defineEmits(["update:modelValue"]);

const formatDateTime = (value) => {
  if (!value) {
    return {
      date: dayjs().format("YYYY-MM-DD"),
      time: dayjs().format("HH:mm"),
    };
  }

  const parsed = dayjs(value);
  if (!parsed.isValid()) {
    return {
      date: dayjs().format("YYYY-MM-DD"),
      time: dayjs().format("HH:mm"),
    };
  }

  return {
    date: parsed.format("YYYY-MM-DD"),
    time: parsed.format("HH:mm"),
  };
};

const dateValue = computed(() => formatDateTime(props.modelValue).date);
const timeValue = computed(() => formatDateTime(props.modelValue).time);

const emitValue = (datePart, timePart) => {
  if (!datePart || !timePart) {
    emit("update:modelValue", "");
    return;
  }

  const [year, month, day] = datePart.split("-");
  if (!year || !month || !day) {
    emit("update:modelValue", "");
    return;
  }

  emit("update:modelValue", `${year}-${month}-${day}T${timePart}:00`);
};

const updateDate = (value) => {
  emitValue(value, timeValue.value);
};

const updateTime = (value) => {
  emitValue(dateValue.value, value);
};
</script>

<style scoped>
.date-time-component {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.date-time-label {
  font-size: 0.95rem;
  color: #333;
}

.date-input,
.time-input {
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
}

@media (max-width: 480px) {
  .date-time-inputs {
    flex-direction: column;
  }

  .date-input,
  .time-input {
    width: 100%;
  }
}
</style>
