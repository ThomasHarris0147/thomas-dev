<template>
  <div class="terminal-window">
    <div class="terminal-header">
      <div class="traffic-lights">
        <span class="red"></span>
        <span class="yellow"></span>
        <span class="green"></span>
      </div>
    </div>

    <div class="terminal-tabs">
      <div
        style="color: grey; display: flex; gap: 10px"
        v-for="(tab, index) in tabs"
        :key="index"
        @click="activeTab = index"
        class="terminal-tab"
        :class="{ active: activeTab === index }"
      >
        {{ tab.name }} <div style="color: white">-zsh</div>
      </div>
    </div>

    <div class="terminal-body">
      <div v-for="(line, i) in tabs[activeTab].content" :key="'static-' + i">
        {{ line }}
      </div>

      <div v-for="(entry, i) in tabs[activeTab].history" :key="'log-' + i">
        <template v-if="entry.type === 'command'">
          <span>{{ entry.text }}</span>
        </template>
        <template v-else-if="entry.type === 'response'">
          <div class="terminal-output" v-html="linkify(entry.text)"></div>
        </template>
        <div v-if="entry.type === 'ascii'" class="ascii-container flex gap-4 mt-4 items-start">
          <pre class="font-mono text-xs leading-none max-w-[50%] overflow-auto">{{ entry.frame }}
          </pre>
          <div class="flex flex-col items-start">
            <button
              v-if="animationComplete && !showOriginalImage"
              @click="showOriginalImage = true"
              class="px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-600 mb-2"
            >
              Show Original Image
            </button>

            <img
              v-if="showOriginalImage"
              :src="imagePath"
              alt="Original"
              class="rounded shadow"
              style="width: 480px;"
            />
          </div>
        </div>
      </div>

      <div class="terminal-input-line">
        <span>$</span>
        <span v-if="!inputValue" class="cursor"></span>
        <input
          ref="inputRef"
          v-model="inputValue"
          class="terminal-input"
          autocomplete="off"
          @keydown="handleCommandSubmit"
        />
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, onMounted, nextTick } from 'vue'

const inputValue = ref('')
const inputRef = ref(null)
const showOriginalImage = ref(false)
const animationComplete = ref(false)
import imagePath from '../assets/picture.jpg'

onMounted(() => {
  inputRef.value?.focus()
})

nextTick(() => {
  inputRef.value?.focus()
})

const tabs = ref([
  {
    name: '~/hello - ',
    content: ['Hello, world!', 'Type /help to get started'],
    history: []
  },
  {
    name: '~/work - ',
    content: [
      'Senior Software Engineer @ The Gang Technology Co., Ltd. 2022-Present',
      '- Optimization and development of a few data extraction projects.',
      '- Managed a small team of developers for a few data extraction projects.',
      '- Handled the data pipeline structure and deployment.',
      '- Worked on both front end code (custom graphs etc) and back end code (django and data extractions). Whether it be data engineering or developing custom power bi visuals using javascript.',
      '- Developed internal and external tools like https://pypi.org/project/exco/ and promotion-optimization that are heavily used in other projects.',
      '------------------------',
      'Freelance English-Thai Translator 2022-2023',
      '- Translated 1000+ words from Thai to English and vice versa.',
      '------------------------',
      'Intern @ MK Research Lab 2022',
      '- Worked with clients to build a few web 3 technologies',
      '------------------------',
      'Backend Intern @ Agoda 2021',
      '- Gateway Migration team',
      '- A/B testing',
      '- Api rerouting'
    ],
    history: []
  }
])

const activeTab = ref(0)

function handleCommandSubmit(e) {
  if (e.key === 'Enter') {
    const command = inputValue.value.trim()
    if (!command) return

    const history = tabs.value[activeTab.value].history

    history.push({ type: 'command', text: `$ ${command}` })
    history.push({ type: 'response', text: simulateOutput(command) })

    inputValue.value = ''
  }
}

function linkify(text) {
  const urlRegex = /(\bhttps?:\/\/[^\s]+)/g
  const emailRegex = /([a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})/g

  return text
    .replace(urlRegex, '<a href="$1" target="_blank" rel="noopener noreferrer">$1</a>')
    .replace(emailRegex, '<a href="mailto:$1">$1</a>')
    .replace(/\n/g, '<br>')
}


function simulateOutput(command) {
  if (command === '/help') {
    return 'Available commands:\n/help - show this help\n/about - about this app\n/contact - contact me\n/picture - show picture'
  } else if (command === '/about') {
    return 'Welcome to my website, my name is Thomas Saran Timmothy Harris. I am a software engineer based in Chiang Mai, Thailand.'
  } else if (command == '/contact') {
    return ('GitHub: https://github.com/ThomasHarris0147\nEmail: thomasharris0147@gmail.com\nLinkedIn: https://www.linkedin.com/in/thomas-saran-timmothy-harris/')
  } else if (command == '/picture') {
    animateAscii()
    return 'Rendering image...'
  }else {
    return `Command not found: ${command}`
  }
}

const asciiArt = [
  "++++*##*%@@@%####%%+====*%#+===+%@*====***+==#%%%@",
  "****+###%%#*+==+=#++*+**##*#*+*+*%%%++=***==*#%##*",
  "###+*#**####+==+#*##@@%%##%*=+*%%@%#*+*=--++=+*###",
  "%%%#+#**##**++*#####*****+==++=++%%###*-==+###+===",
  "#%%%#%##%##########%#%**#*++***#++##*#%+=+===#*===",
  "#*%##%###*#=--==%#%*#####*#***#%##+*+#%######%#===",
  "#%%#+**##%%%*=-==#*+*%%%%%%@@@@%@%***#%%*#*+#@@%##",
  "%###==+*=-+%#==--=**%%%@@@@@@@@@@#*%@%**+===%%%@@@",
  "*#%#**##*+===-=+++#%@@@@@@@@@%##*%%%#*###*#%@@%%%%",
  "%%#**+**#*+++=+**#%@@%@@@%@%%*===+%#****%%%#**#%%%",
  "%%%#########*++*%%@@@@@%%%@%*===+****####**##***##",
  "#+**##%%#%**%%#%%%@@@@@+=+=++---=++##%##=+*#%@@#%%",
  "****#%%%%#**#%@%*+#@@@#==*=--======+%*###@#**%%%%%",
  "*****#%@%#***%%%%*%@@@*===+=======+*#*+#%%%%%@#*##",
  "%#**####**##**%%%%%@@#=--==========**%#*+###+***+-",
  "%*#%%%%%###%%##***#%+-----==+++++++*#***###*=--===",
  "#*#%%%%%*+#%%###++**=-=--==++++**#%#%%*%#*#%%%**++",
  "#*%##%*%#%%@%%#%#+::-=---==++*%+=%@%%%@%*%%%%*###%",
  "===++**%%#%%#=:::::::::--=+++*---+%%%%###*#####+==",
  "**+++*#**+-::::::::::::::===+-:---:::+#*=--+###%##",
  "=*#*+#*+::::::-::::::::::::=::==-:::::::==++==+%%%",
  "##++**=:::::::-:::::-:::::::-=---:::::-::=###%%%@%",
  "#%%%+-:::::::-:-::::-::::-:----:::::---::-*@@%##%@",
  "#%#*=::::::-:-:-:::::::::---:-:::::::---:--*%%#%%@",
  "#%**:::::::::-:::::::-:::---:::-::--:------=###+*%",
  "#%@*:::::::--=-::-:-::::----:::-::::--------*%#*=+",
  "=*%=:::::::--=-:---::::------::--::----------%%*#%",
  "**%-:::::::----:--:::::----=-:---:::-----=----#%@%",
  "***-:::::::---=--:::::-----=::----------==----=+%#",
  "%%#:::::::----=-::-::------=::----------==-----+*%",
  "%#*:::::::---==----:----:--=-:----:------+-----:++",
  "#*+:::::::---=+----===--:--=--:----------+=-::-**+",
  "@#**+=-::::---*=-===--::--==--:--=-------**+==++##",
  "#***=====++++**+++--::::-----:----:------*+===+***",
  "###+=======++*#*+---:::------:----:-----=+===++**+"
]



function animateAscii() {
  const height = asciiArt.length
  const width = asciiArt[0].length
  const charset = '@#%+=-:. '

  let currentFrame = Array.from({ length: height }, () =>
    Array.from({ length: width }, () => randomChar(charset))
  )

  const idx = tabs.value[activeTab.value].history.length
  tabs.value[activeTab.value].history.push({
    type: 'ascii',
    frame: ''
  })

  const interval = setInterval(() => {
    let done = true

    for (let row = 0; row < height; row++) {
      for (let col = 0; col < width; col++) {
        const target = asciiArt[row][col]
        const current = currentFrame[row][col]
        if (current !== target) {
          if (Math.random() < 0.1) {
            currentFrame[row][col] = target
          } else {
            currentFrame[row][col] = randomChar(charset)
            done = false
          }
        }
      }
    }

    tabs.value[activeTab.value].history[idx].frame = currentFrame.map(r => r.join('')).join('\n')

    if (done) {
      clearInterval(interval)
      animationComplete.value = true
    }
  }, 50)
}


function randomChar(charset) {
  return charset[Math.floor(Math.random() * charset.length)]
}

</script>
