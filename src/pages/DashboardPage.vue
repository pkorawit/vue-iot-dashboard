<template>
  <q-page>
    <div class="row q-pa-md">
      <div class="col q-pa-md">
        <q-card dark bordered class="bg-secondary my-card">
          <q-card-section>
            <div class="text-h6">Humidity</div>
            <div class="text-subtitle2">percent</div>
          </q-card-section>

          <q-separator dark inset />

          <q-card-section>
            <div class="text-h3 text-right">{{ dht22.humid }}</div>
          </q-card-section>
        </q-card>
      </div>
      <div class="col q-pa-md">
        <q-card dark bordered class="bg-primary my-card">
          <q-card-section>
            <div class="text-h6">Temperature</div>
            <div class="text-subtitle2">celsius</div>
          </q-card-section>

          <q-separator dark inset />

          <q-card-section>
            <div class="text-h3 text-right">{{ dht22.temp }}</div>
          </q-card-section>
        </q-card>
      </div>
    </div>
    <!-- <div class="row q-pa-md">
      <div class="col q-pa-md">
        <q-card dark bordered class="bg-info my-card">
          <q-card-section>
            <div class="text-h6">Sound</div>
            <div class="text-subtitle2">play sound</div>
          </q-card-section>

          <q-separator dark inset />

          <q-card-section>
            <div class="text-h3 text-right">
              <q-btn @click="playSound" push color="white" text-color="primary" label="Push" />
            </div>
          </q-card-section>
        </q-card>
      </div>
      <div class="col q-pa-md">
        <q-card dark bordered class="bg-warning my-card">
          <q-card-section>
            <div class="text-h6">Humidity</div>
            <div class="text-subtitle2">percent</div>
          </q-card-section>

          <q-separator dark inset />

          <q-card-section>
            <div class="text-h3 text-right">
              <q-btn push color="white" text-color="primary" label="Play" />
            </div>
          </q-card-section>
        </q-card>
      </div>
    </div> -->
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import * as mqtt from "mqtt/dist/mqtt";

export default defineComponent({
  name: "DashboardPage",
  created() {
    const clientId = "mqttjs_" + Math.random().toString(8);
    const host = "ws://broker.hivemq.com:8000/mqtt";
    const options = {
      keepalive: 30,
      clientId: clientId,
    };
    console.log("connecting mqtt client");
    this.client = mqtt.connect(host, options);
    this.client.subscribe("/manalab/dht22/", (err) => {
      if (!err) {
        this.client.on("message", (topic, message) => {
          console.log(topic, message.toString());
          if (topic == "/manalab/dht22/") {
            const values = message.toString().split(",");
            this.dht22.temp = values[1];
            this.dht22.humid = values[0];
          }
        });
      }
    });
  },
  data() {
    return {
      client: null,
      dht22: {
        temp: 0.0,
        humid: 0.0,
      },
    };
  },
  methods: {
    playSound(){
      this.client.publish('/manalab/sound/', '1');
    }
  }
});
</script>
