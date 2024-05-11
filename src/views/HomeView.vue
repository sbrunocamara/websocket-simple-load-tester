<script setup>


</script>

<script>
import moment from 'moment';


export default {

  name: 'websocket-load-tester',


  data() {
    return {
      dataLog: [],
      connecteds: 0,
      statistics: {
        minor: 0,
        major: 0,
        average: 0,
      },
      model: {
        connectionOptionsTogle: 1,
        websocketLogTogle: 0,
        data: '',
        times: 1,
        host: ''
      },
      connections: [],
    }

  },

  methods: {


    async connect() {

      this.model.connectionOptionsTogle = 0;
      this.model.websocketLogTogle = 1;

      for (var i = 0; i < this.model.times; i++) {

        const ws = new WebSocket(this.model.host);

        ws.onopen = () => {
          console.log('Connected to ' + this.model.host);
          this.connections.push(ws);
          ws.send(this.model.data);
          ws.send(this.model.data);
          this.connecteds++;
        };

        ws.onmessage = (event) => {

          this.dataLogging(event.data);

          if (this.connecteds == this.model.times) {
            if (this.dataLog.length == this.model.times) {
              this.dateCompare(this.dataLog);
              this.dataLog = [];
            }

            this.dataLog.push(moment());
          }

        };

        ws.onclose = () => {
          console.log('Disconnected');
        };

      }

    },

    async dateCompare(dataLog) {

      let diferences = [];

      for (let index = 0; index < dataLog.length; index += 2) {

        // Get position of the array
        const date1 = dataLog[index];
        const date2 = dataLog[index + 1];

        if (date1 == undefined || date2 == undefined) {
          return;
        }

        // Calcular a diferença em segundos
        let diffInSeconds = date2.diff(date1, 'seconds', true);

        // Add difference to array
        diferences.push(diffInSeconds);

      }

      this.calculateStatistics(diferences);

    },

    async calculateStatistics(logs) {

      if (logs.length === 0) {
        return false;
      }

      let minor = logs[0];
      let major = logs[0];
      let sum = logs[0];

      for (let i = 1; i < logs.length; i++) {
        if (logs[i] < minor) {
          minor = logs[i];
        }
        if (logs[i] > major) {
          major = logs[i];
        }
        sum += logs[i];
      }

      let average = sum / logs.length;

      minor = minor.toFixed(2);
      major = major.toFixed(2);
      average = average.toFixed(2);

      this.statistics = {
        minor: minor,
        major: major,
        average: average
      };

      return {
        minor: minor,
        major: major,
        average: average
      };
    },

    async dataLogging(data) {
      
      const textarea = this.$refs.textarea;

      // Adicionar a nova mensagem à textarea
      textarea.value += data + '\n';

      // Rolagem automática para a parte inferior
      textarea.scrollTop = textarea.scrollHeight;
    },

    async reset() {
      this.model.connectionOptionsTogle = 1;
      this.model.websocketLogTogle = 0;
      this.connecteds = 0;
      this.statistics = {
        minor: 0,
        major: 0,
        average: 0,
      };

      const textarea = this.$refs.textarea;

      textarea.value = '';

      this.connections.forEach((ws) => {
        console.log('Closing connection');
        ws.close();
      });


    }

  },
  watch: {
    statistics: function (val) {
    }
  },
}


</script>

<template>
  <main>

    <!-- FORM -->
    <form v-show="model.connectionOptionsTogle == 1" @submit.prevent="connect" class="form">
      <div class="mb-3 host">
        <label  for="FormControlInput1" class="form-label">Host</label>
        <input type="text" class="form-control" v-model="model.host" id="host" placeholder="ws://example.com/websocket"
          required>
      </div>

      <div class="mb-3 data">
        <label for="exampleFormControlTextarea1" class="form-label">Opening data</label>
        <textarea class="form-control" id="data" v-model="model.data"
          placeholder="Optional data to send after each succesfull connection" rows="10"></textarea>
      </div>

      <div class="mb-3 times">
        <label for="exampleFormControlTextarea1" class="form-label">Times</label>
        <input type="number" id="times" v-model="model.times" name="tentacles" min="1" max="1000" value="1" />
      </div>


      <!-- Send websocket data -->
      <div class="btn-group" role="group" aria-label="Basic example">
        <button type="submit" class="btn btn-primary send">Send</button>
      </div>
    </form>

    <!-- STATISTICS -->
    <div v-show="model.websocketLogTogle == 1" class="statistics">
      <p>Minor: {{ this.statistics.minor }} seconds</p>
      <p>Major: {{ this.statistics.major }} seconds</p>
      <p>Average: {{ this.statistics.average }} seconds</p>
    </div>

    <!-- LOG -->
    <div v-show="model.websocketLogTogle == 1" class="log">
      <textarea id="textLog" ref="textarea" rows="12" cols="70" readonly></textarea>
    </div>

    <!-- Send websocket data -->
    <div v-show="model.websocketLogTogle == 1" class="btn-group" role="group" aria-label="Basic example">
      <button @click="reset" type="submit" class="btn btn-primary send">Reset</button>
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
  /* justify-content: center; */
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

.statistics {
  display: flex;
  flex-direction: row;
  justify-content: unset;
  margin-top: 15em;

}

.statistics p {
  /* Espaçamento entre os parágrafos */
  background-color: #6e1c9e;
  color: #ffffff;
  border-radius: 1em;
  margin: 0 1.5em;
  padding: 0.5em 1em;

}

.log {
  margin-top: 8em;
  width: 100px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  white-space: nowrap;


}

.log textarea {
  font-size: 16px;
  white-space: nowrap;
  background-color: #c4c2c2;
  color: rgb(0, 0, 0);
  
}

</style>
