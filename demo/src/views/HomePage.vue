<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>DEMO</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content>
      <div
        style="
          display: flex;
          align-items: center;
          justify-content: center;
          text-align: center;
        "
        class="ion-padding"
      >
        <strong v-if="isPlatform('capacitor')">NATIVE MOBILE</strong>
        <strong v-else>WEB BROWSER</strong>
      </div>
      <ion-card>
        <ion-card-content
          style="
            display: flex;
            align-items: center;
            justify-content: center;
            text-align: center;
          "
          class="ion-padding"
        >
          <ion-datetime v-model="dateTime" presentation="time"></ion-datetime>
        </ion-card-content>
        <ion-card-subtitle
          style="
            display: flex;
            align-items: center;
            justify-content: center;
            text-align: center;
          "
          class="ion-padding"
        >
          <ion-input
            v-model="message"
            placeholder="Enter A message: "
          ></ion-input>
          <ion-button @click="setNotif">SET</ion-button>
        </ion-card-subtitle>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import {
  IonInput,
  IonButton,
  IonCardSubtitle,
  IonCardTitle,
  IonCardHeader,
  IonCard,
  IonCardContent,
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
} from "@ionic/vue";
import { isPlatform } from "@ionic/vue";
import { Toast } from "@capacitor/toast";
import { ref } from "vue";
import { LocalNotifications } from "@capacitor/local-notifications";
import { IonDatetime } from "@ionic/vue";

const message = ref("");
// const dateTime = ref(new Date().toLocaleString())
// const dateTime = ref(new Date(Date.now()).toISOString())
const dateTime = ref(
  new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
    .toISOString()
    .slice(0, -1)
);

const setNotif = () => {
  scheduleLocalNotification();
};

const scheduleLocalNotification = async () => {
  if (isPlatform("capacitor")) {
    Toast.show({ text: "set schedule success!" });
    try {
      await LocalNotifications.schedule({
        notifications: [
          {
            title: "Local Notification",
            body: message.value,
            id: 1,
            schedule: { at: new Date(dateTime.value) },
          },
        ],
      });
    } catch (error) {
      console.error("Error scheduling local notification:", error);
    }
  } else {
    const scheduledTime = new Date(dateTime.value);
    const currentTime = new Date();
    const timeDifference = scheduledTime.getTime() - currentTime.getTime();

    console.log("target: " + scheduledTime);
    console.log("current time: " + currentTime);

    if (timeDifference > 0) {
      alert("set schedule success!");
      setTimeout(() => {
        alert("Web Alert: " + message.value);
      }, timeDifference);
    } else {
      console.error("Scheduled time must be in the future.");
    }
  }
};

if (isPlatform("capacitor")) {
  Toast.show({ text: "native mobile" });
} else {
  console.log("web browser");
}
</script>
