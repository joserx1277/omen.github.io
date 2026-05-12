# omen.github.io
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Off-line Dictionary</title>

<style>
body {
  margin: 0;
  padding: 20px;
  font-family: Arial, sans-serif;
  color: #fff;

  background-image:
    linear-gradient(rgba(0,0,0,0.75), rgba(0,0,0,0.9)),
    url("https://images.unsplash.com/photo-1512820790803-83ca734da794");
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
}

.app {
  max-width: 650px;
  margin: auto;
  background: rgba(15,15,15,0.95);
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 0 15px rgba(0,0,0,0.8);
  text-align: center;
}

input, select, button {
  width: 100%;
  padding: 10px;
  margin: 6px 0;
  font-size: 16px;
  border-radius: 8px;
  border: none;
}

input {
  background: #1e1e1e;
  color: white;
}

button, select {
  background: #f5e6d3;
  color: #000000;
  cursor: pointer;
}

.result {
  margin-top: 12px;
  font-size: 18px;
}

.suggestion {
  margin-top: 10px;
  font-size: 14px;
  color: #ffcc80;
  cursor: pointer;
}

body.light .suggestion {
  color: #663300;
}

.saved-list {
  text-align: left;
  margin-top: 15px;
}

.saved-item {
  display: flex;
  justify-content: space-between;
  background: #222;
  padding: 8px;
  margin: 5px 0;
  border-radius: 6px;
}

.delete-btn {
  width: 70px;
  height: 28px;
  font-size: 14px;
  padding: 0;
  margin-right: 5px;
  background: #ff0000;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.reuse-btn {
  width: 70px;
  height: 28px;
  font-size: 14px;
  padding: 0;
  margin-right: 5px;
  background: #556b2f;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
</style>
</head>

<body>

<div class="app">
  <h2>Off-line Dictionary</h2>

  <input id="wordInput" list="suggestions" placeholder="Enter a word..." />
  <datalist id="suggestions"></datalist>

  <button onclick="searchWord()">Search Definition</button>
  <button onclick="speakWord()">Listen to Pronunciation</button>
  <button onclick="speakDefinition()">Listen Definition</button>

 <h3>Translate Word</h3>

  <select id="langSelect">
    <option value="es">Spanish</option>
    <option value="fr">French</option>
    <option value="de">German</option>
    <option value="it">Italian</option>
    <option value="pt">Portuguese</option>
    <option value="ru">Russian</option>
    <option value="zh">Chinese</option>
    <option value="ja">Japanese</option>
    <option value="ko">Korean</option>
    <option value="ar">Arabic</option>
    <option value="hi">Hindi</option>
    <option value="uk">Ukrainian</option>
    <option value="nl">Dutch</option>
    <option value="el">Greek</option>
    <option value="vi">Vietnamese</option>
    <option value="fa">Persian</option>
    <option value="he">Hebrew</option>
  </select>

  <button onclick="translateWord()">Translate</button>

  <div class="result" id="result"></div>
  <div id="suggestionBox"></div>
  <div class="result" id="translationResult"></div>

  <h3>Saved Words</h3>
  <div class="saved-list" id="savedWords"></div>
</div>

<script>
let lastDefinition = "";

/* Offline Mini Dictionary */
const offlineDictionary = {
  hello: "A greeting.",
  world: "The earth and all people on it.",
  book: "A written or printed work of pages.",
  computer: "An electronic device for processing data.",
  language: "A method of communication.",
  airplane: "A powered aircraft with fixed wings.",
  ask: "To request information.",
  be: "To exist or describe identity.",
  begin: "To start something.",
  bring: "To carry something to a place.",
  call: "To speak to someone by phone or name.",
  come: "To move toward a place.",
  do: "To perform an action.",
  feel: "To experience an emotion or sensation.",
  find: "To discover something.",
  get: "To obtain or receive something.",
  give: "To hand something to someone.",
  go: "To move from one place to another.",
  have: "To own or possess something.",
  know: "To understand or be aware of something.",
  look: "To direct your eyes toward something.",
  make: "To create or produce something.",
  need: "To require something.",
  run: "To move quickly on foot.",
  say: "To speak words.",
  see: "To perceive with the eyes.",
  take: "To remove or pick up something.",
  think: "To use the mind to consider or decide.",
  animal: "A living creature that is not a plant.",
  car: "A vehicle used for transportation.",
  city: "A large, populated area with many buildings.",
  child: "A young person.",
  day: "A 24‑hour period.",
  door: "A movable barrier used to enter or exit a space.",
  food: "Something people eat.",
  friend: "A person you trust and enjoy being with.",
  game: "An activity played for fun or competition.",
  hand: "The body part at the end of the arm.",
  home: "The place where someone lives.",
  job: "Work someone does for pay.",
  money: "What people use to buy things.",
  night: "The dark part of the day.",
  people: "Human beings in general.",
  phone: "A device used to talk or send messages.",
  school: "A place where people learn.",
  story: "A tale about events, real or imagined.",
  time: "The ongoing progression of moments.",
  water: "A clear liquid essential for life.",
  air: "The invisible gas we breathe.",
  apple: "A round fruit with sweet flesh.",
  bag: "A container used to carry items.",
  ball: "A round object used in games.",
  beach: "Sandy land next to the ocean.",
  bed: "Furniture used for sleeping.",
  bicycle: "A two‑wheeled vehicle you pedal.",
  bird: "An animal with feathers and wings.",
  boat: "A small watercraft.",
  bottle: "A container for liquids.",
  box: "A container with flat sides.",
  bread: "A baked food made from dough.",
  brother: "A male sibling.",
  building: "A structure with walls and a roof.",
  bus: "A large vehicle for public transport.",
  camera: "A device for taking pictures.",
  cat: "A small domesticated animal.",
  chair: "A seat for one person.",
  cheese: "A dairy food made from milk.",
  clock: "A device that shows time.",
  cloud: "A mass of water vapor in the sky.",
  cookie: "A small sweet baked treat.",
  country: "A nation with its own government.",
  cow: "A large farm animal that gives milk.",
  desk: "A table used for work or study.",
  doctor: "A person trained to treat illness.",
  dog: "A common domesticated animal.",
  earth: "The planet we live on.",
  egg: "A round object laid by birds.",
  eye: "The organ used for seeing.",
  face: "The front part of the head.",
  family: "A group of related people.",
  farm: "Land used for growing food or raising animals.",
  fire: "Heat and light produced by burning.",
  fish: "An animal that lives in water.",
  flower: "The colorful part of a plant.",
  forest: "A large area filled with trees.",
  garden: "A place where plants are grown.",
  glass: "A cup for drinking.",
  grass: "Green plants covering the ground.",
  group: "Several people or things together.",
  hair: "Strands growing from skin.",
  hat: "Clothing worn on the head.",
  head: "The top part of the body.",
  hill: "Raised land smaller than a mountain.",
  house: "A building where people live.",
  island: "Land surrounded by water.",
  key: "A tool used to unlock something.",
  kitchen: "A room where food is prepared.",
  lake: "A large body of fresh water.",
  leg: "A limb used for standing and walking.",
  letter: "A written message.",
  light: "Brightness that lets us see.",
  line: "A long, narrow mark.",
  map: "A drawing that shows locations.",
  market: "A place where goods are sold.",
  milk: "A white liquid from animals.",
  mountain: "A very tall natural elevation.",
  music: "Organ.ized sound for listening.",
  name: "A word used to identify someone.",l
  ocean: "A huge body of saltwater.",
  paper: "Thin material used for writing.",
  parent: "A mother or father.",
  park: "Public land for recreation.",
  plant: "A living thing that grows in soil.",
  river: "Flowing water that moves to the sea.",
  road: "A path for vehicles.",
  room: "A space inside a building.",
  sand: "Tiny grains found on beaches.",
  sea: "A large body of saltwater.",
  shoe: "Clothing worn on the foot.",
  sister: "A female sibling.",
  sky: "The space above earth.",
  sun: "The star that gives earth light.",
  tree: "A tall plant with a trunk.",
  wind: "Moving air.",
  add: "To put things together.",
  answer: "To respond to a question.",
  bake: "To cook in an oven.",
  build: "To construct something.",
  buy: "To pay for something.",
  carry: "To hold and move something.",
  catch: "To grab something moving.",
  change: "To make something different.",
  clean: "To remove dirt.",
  climb: "To move upward.",
  close: "To shut something.",
  cook: "To prepare food with heat.",
  cry: "To shed tears.",
  cut: "To divide with a sharp tool.",
  dance: "To move rhythmically to music.",
  draw: "To make a picture with lines.",
  drink: "To swallow liquid.",
  drive: "To control a vehicle.",
  eat: "To consume food.",
  explain: "To make something clear.",
  fall: "To drop downward.",
  fix: "To repair something broken.",
  fly: "To move through the air.",
  follow: "To go after someone or something.",
  grow: "To become larger.",
  help: "To assist someone.",
  hold: "To grip something.",
  hope: "To wish for something good.",
  hurt: "To cause pain.",
  jump: "To push off the ground.",
  keep: "To retain something.",
  laugh: "To show amusement.",
  learn: "To gain knowledge.",
  leave: "To go away from a place.",
  listen: "To pay attention to sound.",
  live: "To be alive.",
  lose: "To misplace or fail to win.",
  love: "To care deeply for someone.",
  move: "To change position.",
  open: "To make something accessible.",
  pass: "To go by or hand over.",
  play: "To engage in fun activity.",
  pull: "To draw something toward you.",
  push: "To move something away from you.",
  read: "To understand written words.",
  rest: "To relax or stop working.",
  ride: "To travel on something.",
  ring: "To make a bell-like sound.",
  roll: "To move by turning over and over.",
  send: "To cause something to go somewhere.",
  share: "To give part of something to others.",
  shout: "To speak very loudly.",
  sing: "To make musical sounds with your voice.",
  sit: "To rest on your bottom.",
  sleep: "To rest your body and mind.",
  smile: "To show happiness with your face.",
  speak: "To use your voice to communicate.",
  spend: "To use money or time.",
  stand: "To be upright on your feet.",
  start: "To begin an action.",
  stop: "To end an action.",
  study: "To learn by reading or practicing.",
  swim: "To move through water.",
  talk: "To communicate with words.",
  teach: "To help someone learn.",
  tell: "To communicate information.",
  touch: "To make physical contact.",
  try: "To attempt something.",
  turn: "To rotate or change direction.",
  use: "To employ something for a purpose.",
  wait: "To stay in one place until something happens.",
  walk: "To move on foot.",
  watch: "To look at something carefully.",
  win: "To succeed or come first.",
  work: "To perform a task or job.",
  write: "To put words on paper or screen.",
};

/* Load saved words */
window.onload = function() {
  displaySavedWords();
};

async function searchWord() {
  const word = document.getElementById("wordInput").value.trim().toLowerCase();
  const resultDiv = document.getElementById("result");
  const suggestionBox = document.getElementById("suggestionBox");

  suggestionBox.innerHTML = "";
  resultDiv.innerHTML = "Searching...";

  if (!word) {
    resultDiv.innerHTML = "Please type a word.";
    return;
  }

  try {
    const resp = await fetch("https://api.dictionaryapi.dev/api/v2/entries/en/" + word);
    const data = await resp.json();

    if (data.title) {
      resultDiv.innerHTML = "Word not found.";
      showAutoCorrect(word);
    } else {
      const meaning = data[0].meanings[0].definitions[0].definition;
      resultDiv.innerHTML = `<b>${word}</b>: ${meaning}`;
      saveWord(word);
      return;
    }
  } catch {
    resultDiv.innerHTML = "Error loading definition.";
  }

  if (offlineDictionary[word]) {
    lastDefinition = offlineDictionary[word];
    resultDiv.innerHTML = `<b>${word}</b> (Offline): ${offlineDictionary[word]}`;
    saveWord(word);
  } else {
    resultDiv.innerHTML = "Word not found online or offline.";
  }
}

async function showAutoCorrect(word) {
  const suggestionBox = document.getElementById("suggestionBox");

  try {
    const resp = await fetch("https://api.datamuse.com/sug?s=" + word);
    const suggestions = await resp.json();

    if (suggestions.length > 0) {
      const best = suggestions[0].word;
      suggestionBox.innerHTML = `
        <div class="suggestion">
          Did you mean: <b>${best}</b>? (Click to search)
        </div>
      `;
      suggestionBox.onclick = () => {
        document.getElementById("wordInput").value = best;
        searchWord();
      };
    } else {
      suggestionBox.innerHTML = "";
    }
  } catch {
    suggestionBox.innerHTML = "";
  }
}

function speakWord() {
  const word = document.getElementById("wordInput").value.trim();
  if (!word) return;
  speechSynthesis.speak(new SpeechSynthesisUtterance(word));
}

function speakDefinition() {
  if (!lastDefinition) return;
  speechSynthesis.speak(new SpeechSynthesisUtterance(lastDefinition));
}

async function translateWord() {
  const word = document.getElementById("wordInput").value.trim();
  const lang = document.getElementById("langSelect").value;
  const box = document.getElementById("translationResult");

  if (!word) {
    box.innerHTML = "Type a word to translate.";
    return;
  }

  box.innerHTML = "Translating...";

  try {
    const response = await fetch("https://libretranslate.com/translate", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        q: word,
        source: "en",
        target: lang,
        format: "text"
      })
    });

    const data = await response.json();
    box.innerHTML = `<b>Translation:</b> ${data.translatedText}`;
  } catch {
    box.innerHTML = "Translation failed.";
  }
}

/* SAVE WORDS */
function saveWord(word) {
  let saved = JSON.parse(localStorage.getItem("savedWords")) || [];
  if (!saved.includes(word)) {
    saved.push(word);
    localStorage.setItem("savedWords", JSON.stringify(saved));
    displaySavedWords();
  }
}

function displaySavedWords() {
  const container = document.getElementById("savedWords");
  container.innerHTML = "";

  let saved = JSON.parse(localStorage.getItem("savedWords")) || [];

  saved.forEach(word => {
    const div = document.createElement("div");
    div.className = "saved-item";
    div.innerHTML = `
  <span>${word}</span>
  <div>
    <button class="reuse-btn" onclick="reuseWord('${word}')">Reuse</button>
    <button class="delete-btn" onclick="deleteWord('${word}')">Delete</button>
  </div>
`;
    container.appendChild(div);
  });
}

function deleteWord(word) {
  let saved = JSON.parse(localStorage.getItem("savedWords")) || [];
  saved = saved.filter(w => w !== word);
  localStorage.setItem("savedWords", JSON.stringify(saved));
  displaySavedWords();
}

function reuseWord(word) {
  document.getElementById("wordInput").value = word;
  searchWord();
}
</script>

</body>
</html>
