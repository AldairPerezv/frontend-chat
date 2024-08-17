<script lang="ts">
  import { onMount, onDestroy } from "svelte";
  import { getClientId } from "./lib/auth";
  // Importa el cliente Socket.IO
  import io from "socket.io-client";

  // Configura la conexión Socket.IO
  let socket;
  const lengthChars = 12;
  const keyApp = "CHAT_FRONTEND_TOKENxxxxx";
  // Conectar al servidor cuando el componente se monta
  let clientToken;
  onMount(async () => {
    clientToken = localStorage.getItem(keyApp) ?? await getClientId(lengthChars);

    socket = io("https://honey-spectacular-trowel.glitch.me"); // Cambia la URL al servidor de Socket.IO

    // Manejar eventos desde el servidor
    socket.on("message", (data) => {
      console.log("Mensaje del servidor:", data);
    });

    socket.on("connect", () => {
      socket.emit("register", clientToken);
    });

    // Enviar un mensaje al servidor cuando se desconecta
    socket.on("disconnect", () => {
      console.log("Desconectado del servidor");
    });
  });

  // Limpiar la conexión cuando el componente se desmonte
  onDestroy(() => {
    if (socket) {
      socket.disconnect();
    }
  });

  $: if (clientToken) {
    console.log("save", clientToken);
    localStorage.setItem(keyApp, clientToken);
  }

  // Enviar un mensaje al servidor
  function sendMessage() {
    socket.emit("message", "Hola desde Svelte!");
  }
</script>

<main>
  <div class="bg-orange-100 pt-4">
    <div id="chat" class="flex justify-center">
      <div class="flex w-96 bg-orange-400 flex-col rounded">
        <div class="mb-4 bg-white p-2">Cliente:</div>
        <div id="body" class="bg-slate-200 p-2">dddd</div>
        <div id="message" class="flex flex-row bg-slate-400 p-2 gap-2">
          <textarea class="w-full rounded p-2">ss</textarea>
          <button type="button" class="p-2 bg-slate-700 rounded text-white"
            >Enviar</button
          >
        </div>
      </div>
    </div>
  </div>
</main>

<style>
  #chat {
    height: 500px;
  }
  #body {
    height: 380px;
  }
  #message {
    height: 120px;
  }
  #message textarea {
    height: 100%;
  }
</style>
