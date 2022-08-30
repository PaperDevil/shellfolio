<script setup>
import { reactive, ref, onMounted  } from 'vue';

const message_text = ref("");
const logi = reactive({count: 0, arr: []});
let indexing_command = 0;
let command_history = [];
let passed_commands = [];
const fs = reactive({
  file_count: 1,
  files: {
    "links": "t.me/ketovx"
  }
})

function scroll_down() {
  let element = document.getElementById("mainstdout");
  console.log(element.scrollHeight);
  element.scrollTop = element.scrollHeight;
}

function print(text) {
  logi.arr.push(text);
  setTimeout(scroll_down, 1);
}

function about() {
  print("--------- WELCOME MY PORTFOLIO ---------")
  print("1. My name is Danil (Ketov) Gorev")
  print("2. My favorite programming language is Python")
  print("3. My second favorite language is JavaScript <3")
  print("----------------------------------------")
  print("type 'help' for see all comands!")
  print("or type 'clear' for clear this console...")
  print("----------------------------------------")
}

const commands = {
  "clear": {
    "help_text": "clear console :)",
    "call": (args) => {logi.arr = []},
    "visibl": true
  },
  "help": {
    "help_text": "show help",
    "call": (args) => {
      let idx = 0;
      for (const [key, val] of Object.entries(commands)) {
        if (val['visibl']) {
          print(`${idx}. ${key} - ${val['help_text']}`);
          idx++;
        }
      }
    },
    "visibl": true
  },
  "about": {
    "help_text": "about me...",
    "call": (args) => {about()},
    "visibl": true
  },
  "ls": {
    "help_text": "show files in system",
    "call": (args) => {
      if (fs.file_count == 0) {
        print('No files in system now!')
      } else {
        for (const [key, val] of Object.entries(fs.files)) {
          print(key)
        }
      }
    },
    "visibl": false
  },
  "echo": {
    "help_text": "echo something, can create files",
    "call": (args) => {
      if (args.length == 0) print("pass any args please")
      else if (args.length == 3 && args[1] == ">>" || args[1] == '>') {
        if (args[2].length == 0) {
          print("Filename too short...")
        } else {
          fs.files[args[2]] = args[0]
          fs.file_count++;
        }
      } else if (args.length == 1) {
        print(args[0])
      }
    },
    "visibl": false
  },
  "cat": {
    "help_text": "Show content of files",
    "call": (args) => {
      if (args.length == 0) print("give filename")
      else {
        if (args[0] in fs.files) {
          print(fs.files[args[0]])
        } else {
          print('No such file in directory :(')
        }
      }
    },
    "visible": false
  },
  "contact": {
    "help_text": "send me message",
    "call": () => {
      print("<a href='https://t.me/ketovx'>Telegram</a>")
      print("<a href='https://www.instagram.com/ketovx'>Instagram</a>")
      print("<a href='https://github.com/PaperDevil'>Github</a>")
      print("Or send me email <a href='mailto: ketov-x@yandex.ru'>ketov-x@yandex.ru<a/>")
    },
    "visibl": true
  }
}

function send_command() {
  if (message_text.value.trim()) {
    let value = message_text.value.toLowerCase()
    let comline = value.split(' ')
    if (comline[0] in commands) {
      commands[comline[0]]['call'](comline.slice(1));
    } else {
      logi.arr.push(`"${comline[0]}" comand not found!`);
    }
    command_history.push(value);
    message_text.value = "";
  }
}

function autocomplete() {
  for (let [k, v] of Object.entries(commands)) {
    if (k.includes(message_text.value)) {
      message_text.value = k
    }
  }
}

document.addEventListener('keydown', (event) => {
  if (event.code == 'Enter') {
    send_command();
  } else if (event.code == 'ArrowUp') {
    if (command_history.length > 0) {
      let text = command_history.pop()
      message_text.value = text
      passed_commands.push(text)
    }
  } else if (event.code == 'ArrowDown') {
    if (passed_commands.length > 0) {
      let text = passed_commands.pop()
      message_text.value = text
      command_history.push(text)
    } else message_text.value = ""
  } else if (event.code == 'ArrowRight') {
    autocomplete()
  }
});


onMounted(() => {
  about();
})
</script>

<template>
  <div class="console">
    <div class="app-bar">
      <div class="point red"></div>
      <div class="point yellow"></div>
      <div class="point green"></div>
    </div>
    <div class="stdout" id="mainstdout">
      <div class="history" v-if="logi.arr.length > 0">
        <p v-for="com in logi.arr" :key="com" v-html="com">
          
        </p>
      </div>
      <div class="inputs">
        <p>~ </p>
        <input type="text" name="" id="input" v-model="message_text" autofocus @blur="this.focus()">
      </div>
    </div>
  </div>

  <a href="#" @click="send_command()" class="float">
    <i class="fa-solid fa-angles-right my-float"></i>
  </a>
</template>

<style global>
* {
  font-size: 1.2rem;
  color: white;
}

.console {
  padding: 1em;
  width: 40vw;
  height: 45vh;
  border-radius: 10px;
  background: #272822;

  -webkit-box-shadow: 0px 0px 8px 5px rgba(34, 60, 80, 0.2);
  -moz-box-shadow: 0px 0px 8px 5px rgba(34, 60, 80, 0.2);
  box-shadow: 0px 0px 8px 5px rgba(34, 60, 80, 0.2);
}

.app-bar {
  height: 20px;
  display: flex;
  flex-direction: row;
}

.app-bar .point {
  background: #000;
  width: 15px;
  height: 15px;
  border-radius: 50%;
}

.point:not(:first-child) {
  margin-left: 10px;
}

.app-bar .red {
  background: rgb(209, 43, 43);
}

.app-bar .yellow {
  background: rgb(223, 169, 44);
}

.app-bar .green {
  background: rgb(48, 195, 40);
}

.stdout {
  overflow-y: auto;
  width: 100%;
  height: 100%;
}

.history {
  text-align: left;
}

.history p {
  margin: 0;
}

.inputs {
  display: flex;
  flex-direction: row;
}

.inputs p {
  color: yellow;
  margin-right: .5rem;
}

.inputs input {
  border: none;
  background: none;
  outline: none;
  letter-spacing: 2px;
  word-spacing: 4px;
  width: 100%;
}

.float {
  display: none;
}

/* For Desktop View */
@media screen and (min-width: 1024px) {
  .console {
    width: 45vw;
    height: 50vh;
  }
}

/* For Tablet View */
@media screen and (min-device-width: 768px)
and (max-device-width: 1024px) {
  .console {
    width: 80vw;
    height: 80vh;
  }
}

/* For Mobile Portrait View */
@media screen and (max-device-width: 768px){
  * {
    font-size: 1rem;
  }
  #app {
    padding: 0;
    margin: 0;
  }
  .console {
    width: 100vw;
    height: 100vh;
  }
  .float{
    display: block;
    position:fixed;
    width: 60px;
    height: 60px;
    bottom: 40px;
    right: 40px;
    background-color:#0C9;
    color:#FFF;
    border-radius: 50px;
    text-align: center;
  }

  .my-float{
    margin-top:22px;
  }
}
 
/* For Mobile Landscape View */
@media screen and (max-device-width: 640px)
and (orientation: landscape) {
  #app {
    padding: 0;
    margin: 0;
  }
  .console {
    width: 100vw;
    height: 100vh;
  }
}

/* For iPhone 6 and 6 plus Portrait or Landscape View */
@media (min-device-height: 667px) and (min-device-width: 375px)
and (-webkit-min-device-pixel-ratio: 3) {
  #app {
    padding: 0;
    margin: 0;
  }
  .console {
    width: 100vw;
    height: 100vh;
  }
}

</style>
