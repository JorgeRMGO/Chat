<script setup lang="ts">
import type { Ref } from "vue";
// import{ ref } from "vue";

import type { IMessage, IContact } from "@src/types";

import { computed, provide, ref } from "vue";
import useStore from "@src/store/store";

import ChatBottom from "@src/components/views/HomeView/Chat/ChatBottom/ChatBottom.vue";
import ChatMiddle from "@src/components/views/HomeView/Chat/ChatMiddle/ChatMiddle.vue";
import ChatTop from "@src/components/views/HomeView/Chat/ChatTop/ChatTop.vue";

const store = useStore();

// search the selected conversation using activeConversationId.
const activeConversation = computed(() => {
  let activeConversation = store.conversations.find(
    (conversation) => conversation.id === store.activeConversationId
  );

  if (activeConversation) {
    return activeConversation;
  } else {
    return store.archivedConversations.find(
      (conversation) => conversation.id === store.activeConversationId
    );
  }
});

// provide the active conversation to all children.
provide("activeConversation", activeConversation.value);

// determines whether select mode is enabled.
const selectMode = ref(false);

// determines whether all the messages are selected or not.
const selectAll = ref(false);

// holds the selected conversations.
const selectedMessages: Ref<number[]> = ref([]);

// (event) add message to select messages.
const handleSelectMessage = (messageId: number) => {
  selectedMessages.value.push(messageId);

  if (
    activeConversation.value &&
    selectedMessages.value.length === activeConversation.value.messages.length
  ) {
    selectAll.value = true;
  }

  if (!selectMode.value) {
    selectMode.value = true;
  }
};

// (event) remove message from select messages.
const handleDeselectMessage = (messageId: number) => {
  selectAll.value = false;
  selectedMessages.value = selectedMessages.value.filter(
    (item) => item !== messageId
  );

  if (activeConversation.value && selectedMessages.value.length === 0) {
    selectMode.value = false;
  }
};

// (event) select all messages.
const handleSelectAll = () => {
  if (activeConversation.value) {
    const messages = activeConversation.value.messages.map(
      (message) => message.id
    );
    selectedMessages.value = messages;
    selectAll.value = true;
  }
};

// (event) remove the selected messages.
const handleDeselectAll = () => {
  selectAll.value = false;
  selectedMessages.value = [];
};

// (event handle close Select)
const handleCloseSelect = () => {
  selectMode.value = false
  selectAll.value = false;
  selectedMessages.value = [];
};



const jon: IContact = {
  id: 1,
  firstName: 'Jonathan',
  lastName: 'Gutierrez',
  avatar: 'imagen',
  email: 'jonathan.gutierrez@grupo-ortiz.com',
  lastSeen: new Date('2023-09-14'),
};


const mensajes = ref<IMessage[]>([
  {
    id: 1,
    content: "Hola",
    date: '2023-09-09 04:09:10',
    sender: jon,
    //replyTo?: number,
    //previewData?: IPreviewData,
    //attachments?: IAttachment[],
    state: "read",
  },
  {
    id: 2,
    content: "Hola, ¿cómo estás?",
    date: '2023-09-09 04:09:10',
    sender: jon,
    //replyTo?: number,
    //previewData?: IPreviewData,
    //attachments?: IAttachment[],
    state: "read",
  },
  {
    id: 3,
    content: "Hola 2",
    date: '2023-09-09 04:09:10',
    sender: jon,
    //replyTo?: number,
    //previewData?: IPreviewData,
    //attachments?: IAttachment[],
    state: "read",
  },
]);



/*
interface Mensajes {
  id: number;
  content: string;
  sender: "usuario" | "asistente";
}
*/


const sendMessage = ( iMessage: IMessage ) => {
  console.log(iMessage);
  mensajes.value.push( iMessage ); 
};
</script>

<template>
  <div v-if="activeConversation" class="h-full flex flex-col scrollbar-hidden">
    <ChatTop
      :select-all="selectAll"
      :select-mode="selectMode"
      :handle-select-all="handleSelectAll"
      :handle-deselect-all="handleDeselectAll"
      :handle-close-select="handleCloseSelect"
    />
    <ChatMiddle
      :selected-messages="selectedMessages"
      :handle-select-message="handleSelectMessage"
      :handle-deselect-message="handleDeselectMessage"
      :mensajes="mensajes"
    />
    <ChatBottom 
      :send-message="sendMessage"
    />
  </div>
</template>
