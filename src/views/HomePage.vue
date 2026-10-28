<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Prakiraan Cuaca Jakarta</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <div id="container">
        <p>Data Cuaca Per Jam (Open-Meteo)</p>
        <p>
          <ion-button @click="ambilDataCuaca">Ambil Data Cuaca</ion-button>
        </p>

        <table class="center" border="1" cellpadding="8" style="border-collapse: collapse; margin-top: 15px;" v-if="times.length > 0">
          <tr>
            <th>No</th>
            <th>Time (Waktu)</th>
            <th>Temperature (°C)</th>
          </tr>
          <tr v-for="(time, index) in times" :key="index">
            <td>{{ index + 1 }}</td>
            <td>{{ formatWaktu(time) }}</td>
            <td>{{ temperatures[index] }} °C</td>
          </tr>
        </table>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonButton } from '@ionic/vue';
import { defineComponent } from 'vue';
import EndPointAccess from '@/services/EndPointAccess';

export default defineComponent({
  name: 'Home',
  data() {
    return {
      times: [] as string[],
      temperatures: [] as number[]
    }
  },
  methods: {
    // Fungsi untuk memformat timestamp (contoh: 2026-03-29T00:00 menjadi 29 Mar 2026, 00:00)
    formatWaktu(dateTimeStr: string) {
      if (!dateTimeStr) return '';
      const date = new Date(dateTimeStr);
      return date.toLocaleString('id-ID', {
        day: '2-digit',
        month: 'short',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit'
      });
    },

    ambilDataCuaca() {
      const apiURL = 'https://api.open-meteo.com/v1/forecast?latitude=-6.2&longitude=106.8&hourly=temperature_2m';
      const weatherService = new EndPointAccess(apiURL);

      weatherService.getRes()
        .then((response: any) => {
          this.times = response.data.hourly.time.slice(0, 10);
          this.temperatures = response.data.hourly.temperature_2m.slice(0, 10);
        })
        .catch((error) => {
          console.error("Gagal mengambil data cuaca:", error);
        });
    }
  },
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButton
  }
});
</script>

<style scoped>
#container {
  text-align: center;
  padding: 20px;
}
.center {
  margin-left: auto;
  margin-right: auto;
}
</style>
