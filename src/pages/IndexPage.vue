<template>
  <q-page>
    <q-pull-to-refresh @refresh="refresh" color="green">
      <q-card>
        <q-card-section>
          <q-input v-model="tokenDevice" type="text" label="Token device" />
        </q-card-section>
        <q-card-section>
          <q-select
            v-model="exerciseType"
            :options="['correr', 'nadar']"
            label="Tipo de ejercicio"
          />
          <q-input
            v-model="duration"
            label="Duración (minutos)"
            type="number"
          />
          <q-input v-model="distance" label="Distancia (km)" type="number" />
          <br />
          <div class="row">
            <div class="col text-center">
              <q-time v-model="at" color="green-9" />
            </div>
          </div>
          <br />
          <q-btn
            @click="saveExercise"
            label="Guardar ejercicio"
            color="green-8"
            class="fit"
          />
        </q-card-section>
      </q-card>
      <q-list>
        <q-item v-for="exercise in exercises" :key="exercise.id">
          <q-item-section>
            <div>
              {{ exercise.type }} - {{ exercise.duration }} min, A:{{
                exercise.at
              }}
            </div>
          </q-item-section>
        </q-item>
      </q-list>
    </q-pull-to-refresh>
  </q-page>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { PushNotifications } from '@capacitor/push-notifications';
interface exercise {
  id: number;
  type: string;
  duration: number | null;
  distance: number | null;
  at: string | null;
}
export default defineComponent({
  name: 'IndexPage',
  components: {},
  setup() {
    return {
      tokenDevice: ref(''),
      exerciseType: ref(''),
      duration: ref(null),
      distance: ref(null),
      exercises: ref<exercise[]>([]),
      at: ref(null),
    };
  },
  async mounted() {
    await this.requestPushNotificationPermission();
  },
  methods: {
    async requestPushNotificationPermission() {
      const permission = await PushNotifications.requestPermissions();
      if (permission.receive === 'granted') {
        // Si el permiso es concedido, registramos el dispositivo
        await PushNotifications.register();
        this.addPushListeners();
      } else {
        console.log('Permiso de notificaciones no concedido.');
      }
    },
    async addPushListeners() {
      // Escuchar cuando se recibe una notificación push
      PushNotifications.addListener(
        'pushNotificationReceived',
        (notification) => {
          console.log('Notificación recibida:', notification);
          alert(
            `Notificación recibida: ${notification.title} - ${notification.body}`
          );
        }
      );

      // Escuchar cuando se recibe un token (ID único del dispositivo)
      PushNotifications.addListener('registration', (token) => {
        console.log('Token de notificación:', token.value);
        this.tokenDevice = token.value;
        //ffIOouJcRcKC4Ki48OnOEP:APA91bETWQCxEnwoznkdTw8amjBp9rETW2maMCSiGeZrKmglYAbz1Slbr3GSUzRytrNrZv46fhpXFEZ0eIubkKF0-n-EEpqr7xEQ-c27qpA5h7bo4bfFWWs
        // Aquí puedes enviar este token a tu servidor para almacenar y luego enviar notificaciones
      });
    },
    async refresh(done: () => void) {
      await this.addPushListeners();

      done();
    },

    //sendNotification() {},

    saveExercise() {
      const newExercise: exercise = {
        id: Date.now(),
        type: this.exerciseType,
        duration: this.duration,
        distance: this.distance,
        at: this.at,
      };
      this.exercises.push(newExercise);
      this.exerciseType = '';
      this.duration = null;
      this.distance = null;
      this.at = null;
    },
  },
});
</script>
