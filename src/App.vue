<template>
  <ion-app>
    <ion-header>
      <ion-toolbar> Кононец Никита ПР-8</ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-input placeholder=" введите имя" v-model="name"></ion-input>
      <ion-list>
        <ion-item v-for="item in chat">
          <ion-card>
            <ion-card-subtitle>{{ item.name }} в {{ formatDate(new Date(item.time)) }}</ion-card-subtitle>
            <ion-card-content>{{ item.message }}</ion-card-content>
          </ion-card>
        </ion-item>
      </ion-list>

    </ion-content>
    <ion-footer style="display: flex;flex-direction: row;align-items: center; ">
      <ion-input placeholder=" отправить сообщение" id="send" v-model="message"></ion-input>
      <ion-button shape="round"  id='send' @click="sendVal(message,name)">
        <ion-icon :icon="sendSharp"></ion-icon>
      </ion-button>

    </ion-footer>
  </ion-app>
</template>

<script async setup lang="ts">
import {
  alertController,
  IonApp,
  IonButton,
  IonCard,
  IonCardContent,
  IonCardSubtitle,
  IonContent,
  IonFooter,
  IonHeader,
  IonIcon,
  IonInput,
  IonItem,
  IonList,
  IonToolbar
} from '@ionic/vue';
import {sendSharp} from "ionicons/icons";
import {initializeApp} from "firebase/app";
import {collection, doc, getFirestore, onSnapshot, setDoc,query,orderBy} from "firebase/firestore";
import {ref} from "vue";

const firebaseConfig = {
  apiKey: "AIzaSyA09muEM9nEAslD3HaE0hg_4b98pOvcep4",
  authDomain: "web-chat-866fc.firebaseapp.com",
  databaseURL: "https://web-chat-866fc-default-rtdb.europe-west1.firebasedatabase.app/",
  projectId: "web-chat-866fc",
  storageBucket: "web-chat-866fc.appspot.com",
  messagingSenderId: "799650381827",
  appId: "1:799650381827:web:06af85efb9b9ee6566ce36"
};
const app = initializeApp(firebaseConfig);
let DB = getFirestore(app)
let chat = ref([])
let name = ref('')
let message = ref('')

function formatDate(Date: Date) {
  return `${Date.getFullYear()}-${Date.getMonth()}-${Date.getDate()}  ${Date.getHours()}:${Date.getMinutes()}:${Date.getSeconds()}`
}

async function sendVal(message, name): void {
if (message.length===0 || name.length===0){
  await alertController.create({
    header:'ошибка',
    message:'введите сообщение или имя ',
    buttons:['ok']
  }).then(res=> res.present());
}else {
  await setDoc(doc(DB, 'chat', `${window.crypto.randomUUID()}`), {
    name: name,
    time: new Date().getTime(),
    message: message
  });
}
}

onSnapshot(query(collection(DB, 'chat'),orderBy('time','asc')), docs => {
  chat.value = []
  message.value=[]
  docs.forEach(doc => {
    chat.value.push(doc.data())
  })
})


</script>
