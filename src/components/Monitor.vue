<template>
  <div class="root">
    <!-- Top Bar -->
    <div class="top-bar">
      <span class="lbl">Show:</span>
      <div class="mode-group">
        <v-btn
          v-for="m in modes"
          :key="m.value"
          :variant="showMode === m.value ? 'tonal' : 'outlined'"
          size="small"
          @click="showMode = m.value"
        >
          {{ m.label }}
        </v-btn>
      </div>

      <v-divider vertical class="mx-2" />

      <v-text-field
        v-model="newVideoUrl"
        placeholder="Paste video URL…"
        density="compact"
        hide-details
        variant="outlined"
        class="url-input"
      />
      <v-btn
        size="small"
        variant="tonal"
        prepend-icon="mdi-play"
        @click="loadVideoUrl"
        >Load</v-btn
      >
      <v-btn
        size="small"
        variant="outlined"
        prepend-icon="mdi-open-in-new"
        @click="openInNewTab"
        >Open</v-btn
      >
    </div>

    <!-- Panels -->
    <div class="panels" :class="{ single: showMode !== 'all' }">
      <!-- Real Time Panel -->
      <div v-if="showMode !== 'playback'" class="panel">
        <h2>Real time</h2>
        <div class="video-box">
          <iframe
            :src="newVideoUrl"
            width="100%"
            height="100%"
            frameborder="0"
            allowfullscreen
          />
        </div>
        <div class="fields">
          <div class="field">
            <label>Device ID</label>
            <v-text-field
              v-model="payloadState.deviceId"
              density="compact"
              hide-details
              variant="outlined"
            />
          </div>
          <div class="field">
            <label>Channel</label>
            <v-text-field
              v-model="payloadState.chs"
              density="compact"
              hide-details
              variant="outlined"
            />
          </div>
        </div>
        <div class="btns">
          <v-btn
            :color="rtState === 'play' ? 'primary' : undefined"
            :variant="rtState === 'play' ? 'tonal' : 'outlined'"
            icon
            size="small"
            aria-label="Play"
            title="Play"
            @click="rtAction('play')"
          >
            <v-icon>mdi-play</v-icon>
          </v-btn>
          <v-btn
            :color="rtState === 'pause' ? 'primary' : undefined"
            :variant="rtState === 'pause' ? 'tonal' : 'outlined'"
            icon
            size="small"
            aria-label="Pause"
            title="Pause"
            @click="rtAction('pause')"
          >
            <v-icon>mdi-pause</v-icon>
          </v-btn>
          <v-btn
            :color="rtState === 'stop' ? 'error' : undefined"
            :variant="rtState === 'stop' ? 'tonal' : 'outlined'"
            icon
            size="small"
            aria-label="Stop"
            title="Stop"
            @click="rtAction('stop')"
          >
            <v-icon>mdi-stop</v-icon>
          </v-btn>

          <v-divider vertical class="mx-1" />

          <v-btn
            :color="rtSound ? 'primary' : undefined"
            :variant="rtSound ? 'tonal' : 'outlined'"
            icon
            size="small"
            :aria-label="rtSound ? 'Sound on' : 'Sound off'"
            :title="rtSound ? 'Sound on' : 'Sound off'"
            @click="rtSound = !rtSound"
          >
            <v-icon>{{
              rtSound ? "mdi-volume-high" : "mdi-volume-off"
            }}</v-icon>
          </v-btn>
          <v-btn
            icon
            size="small"
            variant="outlined"
            aria-label="Screenshot"
            title="Screenshot"
          >
            <v-icon>mdi-camera</v-icon>
          </v-btn>
          <v-btn
            icon
            size="small"
            variant="outlined"
            aria-label="Fullscreen"
            title="Fullscreen"
          >
            <v-icon>mdi-fullscreen</v-icon>
          </v-btn>
        </div>
      </div>

      <!-- Playback Panel -->
      <div v-if="showMode !== 'real-time'" class="panel">
        <h2>Video playback</h2>
        <div class="video-box">
          <iframe
            :src="newVideoUrl"
            width="100%"
            height="100%"
            frameborder="0"
            allowfullscreen
          />
        </div>
        <div class="fields">
          <div class="field">
            <label>Device ID</label>
            <v-text-field
              v-model="payloadState.deviceId"
              density="compact"
              hide-details
              variant="outlined"
            />
          </div>
          <div class="field">
            <label>Channel</label>
            <v-text-field
              v-model="payloadState.chs"
              density="compact"
              hide-details
              variant="outlined"
            />
          </div>
          <div class="field">
            <label>Start time</label>
            <DateTimeComponent
              v-model="payloadState.startTime"
              label="Start time"
            />
          </div>
          <div class="field">
            <label>End time</label>
            <DateTimeComponent
              v-model="payloadState.endTime"
              label="End time"
            />
          </div>
        </div>
        <div class="btns">
          <v-btn
            :color="pbState === 'play' ? 'primary' : undefined"
            :variant="pbState === 'play' ? 'tonal' : 'outlined'"
            icon
            size="small"
            aria-label="Playback"
            title="Playback"
            @click="pbAction('play')"
          >
            <v-icon>mdi-play</v-icon>
          </v-btn>
          <v-btn
            :color="pbState === 'pause' ? 'primary' : undefined"
            :variant="pbState === 'pause' ? 'tonal' : 'outlined'"
            icon
            size="small"
            aria-label="Pause"
            title="Pause"
            @click="pbAction('pause')"
          >
            <v-icon>mdi-pause</v-icon>
          </v-btn>
          <v-btn
            :color="pbState === 'stop' ? 'error' : undefined"
            :variant="pbState === 'stop' ? 'tonal' : 'outlined'"
            icon
            size="small"
            aria-label="Stop"
            title="Stop"
            @click="pbAction('stop')"
          >
            <v-icon>mdi-stop</v-icon>
          </v-btn>

          <v-divider vertical class="mx-1" />

          <v-btn
            v-for="s in speeds"
            :key="s"
            :color="pbSpeed === s ? 'primary' : undefined"
            :variant="pbSpeed === s ? 'tonal' : 'outlined'"
            size="small"
            :aria-label="`Speed ×${s}`"
            :title="`×${s}`"
            @click="pbSpeed = s"
          >
            ×{{ s }}
          </v-btn>

          <v-divider vertical class="mx-1" />

          <v-btn
            :color="pbSound ? 'primary' : undefined"
            :variant="pbSound ? 'tonal' : 'outlined'"
            icon
            size="small"
            :aria-label="pbSound ? 'Sound on' : 'Sound off'"
            :title="pbSound ? 'Sound on' : 'Sound off'"
            @click="pbSound = !pbSound"
          >
            <v-icon>{{
              pbSound ? "mdi-volume-high" : "mdi-volume-off"
            }}</v-icon>
          </v-btn>
          <v-btn
            icon
            size="small"
            variant="outlined"
            aria-label="Screenshot"
            title="Screenshot"
          >
            <v-icon>mdi-camera</v-icon>
          </v-btn>
          <v-btn
            icon
            size="small"
            variant="outlined"
            aria-label="Fullscreen"
            title="Fullscreen"
          >
            <v-icon>mdi-fullscreen</v-icon>
          </v-btn>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, watch, onMounted } from "vue";
import DateTimeComponent from "./input/DateTimeComponent.vue";

const props = defineProps({
  videoType: {
    type: String,
    default: "",
  },
  payload: {
    type: Object,
    default: () => ({}),
  },
});

const modes = [
  { label: "Real time", value: "real-time" },
  { label: "Playback", value: "playback" },
];

const speeds = [0, 1, 2, 4, 8, 16];

// ── State ─────────────────────────────────────────────────────────────────────
const showMode = ref("all");

const rtState = ref("play"); // "play" | "pause" | "stop"
const rtSound = ref(true);

const pbState = ref("play"); // "play" | "pause" | "stop"
const pbSound = ref(true);
const pbSpeed = ref(0);

const payloadState = reactive({
  deviceId: props.payload.deviceId || "",
  chs: props.payload.chs || "",
  startTime: props.payload.startTime || "",
  endTime: props.payload.endTime || "",
});

const decodeVideoUrl = (url) => {
  try {
    return url;
  } catch {
    return url;
  }
};

const newVideoUrl = ref(props.payload.videoUrl || "");
const videoSrc = ref(newVideoUrl.value ? decodeVideoUrl(newVideoUrl.value) : "");


const updateUrl = () => {
  const params = new URLSearchParams();

  if (payloadState.deviceId) params.set("deviceId", payloadState.deviceId);
  if (payloadState.chs) params.set("chs", payloadState.chs);
  if (payloadState.startTime) params.set("startTime", payloadState.startTime);
  if (payloadState.endTime) params.set("endTime", payloadState.endTime);
  if (newVideoUrl.value) params.set("videoUrl", newVideoUrl.value);

  const query = params.toString();
  const newUrl = `${window.location.pathname}${query ? `?${query}` : ""}${window.location.hash}`;
  window.history.replaceState(null, "", newUrl);
};

const loadVideoUrl = () => {
  if (!newVideoUrl.value) return;
  videoSrc.value = decodeVideoUrl(newVideoUrl.value);
};

const openInNewTab = () => {
  const url = newVideoUrl.value || videoSrc.value;
  try {
    window.open(url, "_blank");
  } catch (e) {
    console.warn(e);
  }
};

const rtAction = (state) => {
  rtState.value = state;
};
const pbAction = (state) => {
  pbState.value = state;
};

const initialize = () => {
  if (props.videoType === "RealVideo") {
    showMode.value = "real-time";
  } else if (props.videoType === "Playback") {
    showMode.value = "playback";
  }
  updateUrl();
};

watch(
  [
    () => newVideoUrl.value,
    () => payloadState.deviceId,
    () => payloadState.chs,
    () => payloadState.startTime,
    () => payloadState.endTime,
  ],
  updateUrl,
);

onMounted(() => {
  initialize();
});
</script>

<style scoped>
.root {
  display: flex;
  flex-direction: column;
  gap: 8px;
  padding: 8px;
  height: 100vh;
  min-height: 600px;
  font-family: Arial, sans-serif;
}

/* ── Top bar ─────────────────────────────────────────── */
.top-bar {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  background: #f5f7fa;
  border: 1px solid #e1e5eb;
  border-radius: 10px;
  flex-shrink: 0;
}

.lbl {
  font-size: 12px;
  font-weight: 600;
  color: #334155;
  white-space: nowrap;
}

.mode-group {
  display: flex;
  gap: 4px;
}

.url-input {
  flex: 1;
  min-width: 200px;
}

/* ── Panels grid ─────────────────────────────────────── */
.panels {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 8px;
  flex: 1;
  min-height: 0;
  margin: 0 150px; /* Center panels with max width */
  height: 100%;
}

.panels.single {
  grid-template-columns: 1fr;
}

/* ── Individual panel ────────────────────────────────── */
.panel {
  display: flex;
  flex-direction: column;
  gap: 6px;
  background: #fff;
  border: 1px solid #e1e5eb;
  border-radius: 12px;
  padding: 10px;
  overflow: hidden;
}

.panel h2 {
  font-size: 13px;
  font-weight: 600;
  color: #64748b;
  flex-shrink: 0;
  margin: 0;
}

/* ── Video ───────────────────────────────────────────── */
.video-box {
  flex: 1;
  min-height: 0;
  border-radius: 8px;
  background: #000;
  overflow: hidden;
  position: relative;
}

.video-box iframe {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  border: 0;
}

/* ── Fields ──────────────────────────────────────────── */
.fields {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 6px;
  flex-shrink: 0;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.field label {
  font-size: 11px;
  color: #64748b;
}

/* ── Button row ──────────────────────────────────────── */
.btns {
  display: flex;
  flex-wrap: wrap;
  gap: 4px;
  align-items: center;
  flex-shrink: 0;
}

/* ── Responsive ──────────────────────────────────────── */
@media (max-width: 960px) {
  .panels {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 640px) {
  .fields {
    grid-template-columns: 1fr;
  }

  .top-bar {
    flex-wrap: wrap;
  }

  .url-input {
    min-width: 100%;
    order: 1;
  }
}
</style>
