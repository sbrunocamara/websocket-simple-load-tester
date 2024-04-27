<script setup>


</script>

<script>
import * as bootstrap from 'bootstrap';


export default {

  name: 'websocket-load-tester',


  methods: {

    async connect() {
      const host = document.getElementById('host').value;
      const data = document.getElementById('data').value;
      const times = document.getElementById('times').value;


      // let ws = [];

      
      for (var i = 0; i < times; i++) {

       const ws = new WebSocket(host);

        ws.onopen = () => {
          console.log('Connected to ' + host);
          ws.send(data);
        };

        ws.onmessage = (event) => {
          console.log('Data received');
        };

        ws.onclose = () => {
          console.log('Disconnected');
        };

      }



    },

  }
}




</script>

<template>
  <main>

    <!-- FORM -->
    <form @submit.prevent="connect" class="form">
      <div class="mb-3 host">
        <label for="exampleFormControlInput1" class="form-label">Host</label>
        <input type="text" class="form-control" id="host" placeholder="ws://example.com/websocket"  required>
      </div>

      <div class="mb-3 data">
        <label for="exampleFormControlTextarea1" class="form-label">Opening data</label>
        <textarea class="form-control" id="data"  placeholder="Optional data to send after each succesfull connection" rows="10"></textarea>
      </div>

      <div class="mb-3 times">
        <label for="exampleFormControlTextarea1" class="form-label">Times</label>
        <input type="number" id="times" name="tentacles"  min="1" max="1000" value="1" />
      </div>


      <!-- Send websocket data -->
      <div class="btn-group" role="group" aria-label="Basic example">
        <button type="submit" class="btn btn-primary send">Send</button>
      </div>
    </form>

    <div class="HolderRow" v-for="(row, rowindex) in 9" :key="rowindex">
    <Holder
        v-for="(holder, holderindex) in 11"
        :key="holderindex"
        :holder="holder + (11 * rowindex)"
    />
</div>


  </main>

</template>

<style scoped>
.send {
  color: #ffffff;
  font-family: 'Poppins', sans-serif;
  padding: 0.6em 0.90em;
  border-radius: 1em;
  background-color: #6e1c9e;
  border: none;
  margin-top: 1em;
  /* margin: 2em 1em */

}

textarea {
  resize: both;
}

.send:hover {
  background-color: #620499;
}

.send:active {
  background-color: #9908ec;
}

.btn-group {
  display: flex;
  justify-content: center;
  align-items: center;

}

main {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #2c2c2c;
}

.form-label {
  color: #ffffff;
  font-family: 'Poppins', sans-serif;
}


.host {
  margin-top: 10em;
  width: 20em;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  white-space: nowrap;

}

label {
  background-color: #6e1c9e;
  width: 50%;
  text-align: center;
  border-radius: 1em;
  /* margin: 0; */

}

.data {
  margin-top: 1em;
  width: 20em;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  white-space: nowrap;


}

.times {
  margin-top: 1em;
  width: 5.5em;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  white-space: nowrap;
}

.times input {
  width: 100%;
  text-align: center;
  border-radius: 0.5em;
  margin: 0;
  
}

.form {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 60%;
  height: 100%;
  padding: 2em;

}

.times label {
  width: 5em;

}
</style>
