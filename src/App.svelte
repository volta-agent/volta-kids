<script>
 import { onMount } from 'svelte';
 
 let currentView = $state('home');
 let currentCategory = $state(null);
 let currentItem = $state(0);
 let score = $state(0);
 let showCelebration = $state(false);
 let celebrationType = $state('correct');
 let matchedPairs = $state([]);
 let selectedCard = $state(null);
 
 // Progress tracking per category
 let categoryProgress = $state({});
 
 const categories = [
 {
 id: 'colors',
 name: 'Colors',
 emoji: '🌈',
 color: '#FF6B6B',
 words: [
 { word: 'Red', emoji: '🔴', color: '#FF0000' },
 { word: 'Blue', emoji: '🔵', color: '#0000FF' },
 { word: 'Green', emoji: '🟢', color: '#00FF00' },
 { word: 'Yellow', emoji: '🟡', color: '#FFFF00' },
 { word: 'Orange', emoji: '🟠', color: '#FFA500' },
 { word: 'Purple', emoji: '🟣', color: '#800080' },
 { word: 'Pink', emoji: '💗', color: '#FF69B4' },
 { word: 'Black', emoji: '⚫', color: '#000000' },
 { word: 'White', emoji: '⚪', color: '#FFFFFF' },
 { word: 'Brown', emoji: '🟤', color: '#8B4513' }
 ]
 },
 {
 id: 'animals',
 name: 'Animals',
 emoji: '🦁',
 color: '#4ECDC4',
 words: [
 { word: 'Dog', emoji: '🐕' },
 { word: 'Cat', emoji: '🐱' },
 { word: 'Bird', emoji: '🐦' },
 { word: 'Fish', emoji: '🐟' },
 { word: 'Lion', emoji: '🦁' },
 { word: 'Elephant', emoji: '🐘' },
 { word: 'Rabbit', emoji: '🐰' },
 { word: 'Bear', emoji: '🐻' },
 { word: 'Duck', emoji: '🦆' },
 { word: 'Cow', emoji: '🐄' }
 ]
 },
 {
 id: 'numbers',
 name: 'Numbers',
 emoji: '🔢',
 color: '#FFE66D',
 words: [
 { word: 'One', emoji: '1️⃣', value: 1 },
 { word: 'Two', emoji: '2️⃣', value: 2 },
 { word: 'Three', emoji: '3️⃣', value: 3 },
 { word: 'Four', emoji: '4️⃣', value: 4 },
 { word: 'Five', emoji: '5️⃣', value: 5 },
 { word: 'Six', emoji: '6️⃣', value: 6 },
 { word: 'Seven', emoji: '7️⃣', value: 7 },
 { word: 'Eight', emoji: '8️⃣', value: 8 },
 { word: 'Nine', emoji: '9️⃣', value: 9 },
 { word: 'Ten', emoji: '🔟', value: 10 }
 ]
 },
 {
 id: 'shapes',
 name: 'Shapes',
 emoji: '🔷',
 color: '#A8E6CF',
 words: [
 { word: 'Circle', emoji: '⭕' },
 { word: 'Square', emoji: '⬜' },
 { word: 'Triangle', emoji: '🔺' },
 { word: 'Star', emoji: '⭐' },
 { word: 'Heart', emoji: '❤️' },
 { word: 'Diamond', emoji: '💎' },
 { word: 'Moon', emoji: '🌙' },
 { word: 'Cross', emoji: '✚' }
 ]
 },
 {
 id: 'family',
 name: 'Family',
 emoji: '👨‍👩‍👧‍👦',
 color: '#DDA0DD',
 words: [
 { word: 'Mom', emoji: '👩' },
 { word: 'Dad', emoji: '👨' },
 { word: 'Baby', emoji: '👶' },
 { word: 'Grandma', emoji: '👵' },
 { word: 'Grandpa', emoji: '👴' },
 { word: 'Sister', emoji: '👧' },
 { word: 'Brother', emoji: '👦' },
 { word: 'Family', emoji: '👨‍👩‍👧‍👦' }
 ]
 },
 {
 id: 'body',
 name: 'Body',
 emoji: '👂',
 color: '#87CEEB',
 words: [
 { word: 'Head', emoji: '🗣️' },
 { word: 'Eye', emoji: '👁️' },
 { word: 'Ear', emoji: '👂' },
 { word: 'Nose', emoji: '👃' },
 { word: 'Mouth', emoji: '👄' },
 { word: 'Hand', emoji: '✋' },
 { word: 'Foot', emoji: '🦶' },
 { word: 'Heart', emoji: '❤️' }
 ]
 },
 {
 id: 'food',
 name: 'Food',
 emoji: '🍎',
 color: '#FFB347',
 words: [
 { word: 'Apple', emoji: '🍎' },
 { word: 'Banana', emoji: '🍌' },
 { word: 'Bread', emoji: '🍞' },
 { word: 'Milk', emoji: '🥛' },
 { word: 'Egg', emoji: '🥚' },
 { word: 'Cheese', emoji: '🧀' },
 { word: 'Pizza', emoji: '🍕' },
 { word: 'Cookie', emoji: '🍪' }
 ]
 },
 // NEW CATEGORIES
 {
 id: 'actions',
 name: 'Actions',
 emoji: '🏃',
 color: '#E87722',
 words: [
 { word: 'Run', emoji: '🏃' },
 { word: 'Jump', emoji: '🦘' },
 { word: 'Walk', emoji: '🚶' },
 { word: 'Swim', emoji: '🏊' },
 { word: 'Dance', emoji: '💃' },
 { word: 'Sing', emoji: '🎤' },
 { word: 'Read', emoji: '📖' },
 { word: 'Write', emoji: '✏️' },
 { word: 'Sleep', emoji: '😴' },
 { word: 'Eat', emoji: '🍽️' }
 ]
 },
 {
 id: 'clothes',
 name: 'Clothes',
 emoji: '👕',
 color: '#9B59B6',
 words: [
 { word: 'Shirt', emoji: '👕' },
 { word: 'Pants', emoji: '👖' },
 { word: 'Dress', emoji: '👗' },
 { word: 'Shoes', emoji: '👟' },
 { word: 'Hat', emoji: '🎩' },
 { word: 'Coat', emoji: '🧥' },
 { word: 'Socks', emoji: '🧦' },
 { word: 'Gloves', emoji: '🧤' }
 ]
 },
 {
 id: 'weather',
 name: 'Weather',
 emoji: '🌤️',
 color: '#3498DB',
 words: [
 { word: 'Sunny', emoji: '☀️' },
 { word: 'Rainy', emoji: '🌧️' },
 { word: 'Cloudy', emoji: '☁️' },
 { word: 'Snowy', emoji: '❄️' },
 { word: 'Windy', emoji: '💨' },
 { word: 'Stormy', emoji: '⛈️' },
 { word: 'Rainbow', emoji: '🌈' },
 { word: 'Hot', emoji: '🥵' }
 ]
 },
 {
 id: 'vehicles',
 name: 'Vehicles',
 emoji: '🚗',
 color: '#27AE60',
 words: [
 { word: 'Car', emoji: '🚗' },
 { word: 'Bus', emoji: '🚌' },
 { word: 'Train', emoji: '🚂' },
 { word: 'Plane', emoji: '✈️' },
 { word: 'Boat', emoji: '🚤' },
 { word: 'Bike', emoji: '🚲' },
 { word: 'Truck', emoji: '🚛' },
 { word: 'Helicopter', emoji: '🚁' }
 ]
 }
 ];
 
 let synth = null;
 let voices = [];
 let audioContext = null;
 
 onMount(() => {
 if (typeof window !== 'undefined' && window.speechSynthesis) {
 synth = window.speechSynthesis;
 voices = synth.getVoices();
 if (voices.length === 0) {
 synth.onvoiceschanged = () => { voices = synth.getVoices(); };
 }
 }
 
 // Load saved score
 const savedScore = localStorage.getItem('volta-kids-score');
 if (savedScore) score = parseInt(savedScore);
 
 // Load saved progress
 const savedProgress = localStorage.getItem('volta-kids-progress');
 if (savedProgress) categoryProgress = JSON.parse(savedProgress);
 });
 
 // Sound effects using Web Audio API
 function playSound(type) {
 if (!audioContext) {
 audioContext = new (window.AudioContext || window.webkitAudioContext)();
 }
 
 const oscillator = audioContext.createOscillator();
 const gainNode = audioContext.createGain();
 
 oscillator.connect(gainNode);
 gainNode.connect(audioContext.destination);
 
 if (type === 'correct') {
 // Happy ascending notes
 oscillator.frequency.setValueAtTime(523.25, audioContext.currentTime); // C5
 oscillator.frequency.setValueAtTime(659.25, audioContext.currentTime + 0.1); // E5
 oscillator.frequency.setValueAtTime(783.99, audioContext.currentTime + 0.2); // G5
 gainNode.gain.setValueAtTime(0.3, audioContext.currentTime);
 gainNode.gain.exponentialDecayTo && gainNode.gain.exponentialDecayTo(0.01, audioContext.currentTime + 0.4);
 } else if (type === 'wrong') {
 // Sad descending buzz
 oscillator.frequency.setValueAtTime(300, audioContext.currentTime);
 oscillator.frequency.setValueAtTime(200, audioContext.currentTime + 0.15);
 gainNode.gain.setValueAtTime(0.3, audioContext.currentTime);
 } else if (type === 'celebrate') {
 // Fanfare
 oscillator.frequency.setValueAtTime(523.25, audioContext.currentTime);
 oscillator.frequency.setValueAtTime(659.25, audioContext.currentTime + 0.1);
 oscillator.frequency.setValueAtTime(783.99, audioContext.currentTime + 0.2);
 oscillator.frequency.setValueAtTime(1046.50, audioContext.currentTime + 0.3);
 gainNode.gain.setValueAtTime(0.4, audioContext.currentTime);
 }
 
 gainNode.gain.setValueAtTime(gainNode.gain.value, audioContext.currentTime + 0.3);
 gainNode.gain.linearRampToValueAtTime(0, audioContext.currentTime + 0.5);
 
 oscillator.start(audioContext.currentTime);
 oscillator.stop(audioContext.currentTime + 0.5);
 }
 
 function speak(text) {
 if (!synth) return;
 synth.cancel();
 const utterance = new SpeechSynthesisUtterance(text);
 utterance.lang = 'en-US';
 utterance.rate = 0.8;
 const voice = voices.find(v => v.lang.includes('en'));
 if (voice) utterance.voice = voice;
 synth.speak(utterance);
 }
 
 function celebrate(type = 'correct') {
 celebrationType = type;
 showCelebration = true;
 if (type === 'celebrate') {
 playSound('celebrate');
 } else {
 playSound('correct');
 }
 score += 5;
 localStorage.setItem('volta-kids-score', score);
 setTimeout(() => { showCelebration = false; }, 2000);
 }
 
 function saveProgress(categoryId, activity, correct, total) {
 if (!categoryProgress[categoryId]) {
 categoryProgress[categoryId] = { flashcards: false, matching: false, quiz: 0, tracing: false };
 }
 
 if (activity === 'flashcards') {
 categoryProgress[categoryId].flashcards = true;
 } else if (activity === 'matching') {
 categoryProgress[categoryId].matching = true;
 } else if (activity === 'quiz') {
 const prev = categoryProgress[categoryId].quiz || 0;
 categoryProgress[categoryId].quiz = Math.max(prev, Math.round((correct / total) * 100));
 } else if (activity === 'tracing') {
 categoryProgress[categoryId].tracing = true;
 }
 
 localStorage.setItem('volta-kids-progress', JSON.stringify(categoryProgress));
 }
 
 function getProgressPercent(cat) {
 const progress = categoryProgress[cat.id];
 if (!progress) return 0;
 let total = 0;
 let completed = 0;
 if (progress.flashcards) completed++;
 total++;
 if (progress.matching) completed++;
 total++;
 if (progress.quiz >= 80) completed++;
 total++;
 if (progress.tracing) completed++;
 total++;
 return Math.round((completed / total) * 100);
 }
 
 function nextItem() {
 if (currentCategory && currentItem < currentCategory.words.length - 1) {
 currentItem++;
 speak(currentCategory.words[currentItem].word);
 }
 }
 
 function prevItem() {
 if (currentItem > 0) {
 currentItem--;
 speak(currentCategory.words[currentItem].word);
 }
 }
 
 function openCategory(cat) {
 currentCategory = cat;
 currentItem = 0;
 currentView = 'flashcards';
 setTimeout(() => speak(cat.words[0].word), 300);
 }
 
 function goHome() {
 currentView = 'home';
 currentCategory = null;
 }
 
 // Matching game
 let matchCards = $state([]);
 let matchedCount = $state(0);
 
 function startMatching() {
 if (!currentCategory) return;
 
 const cards = [];
 currentCategory.words.slice(0, 6).forEach((w, i) => {
 cards.push({ id: i, type: 'word', content: w.word, pairId: i });
 cards.push({ id: i + 100, type: 'emoji', content: w.emoji, pairId: i });
 });
 
 matchCards = cards.sort(() => Math.random() - 0.5);
 matchedPairs = [];
 selectedCard = null;
 matchedCount = 0;
 currentView = 'matching';
 }
 
 function selectCard(card) {
 if (matchedPairs.includes(card.id)) return;
 
 if (!selectedCard) {
 selectedCard = card;
 speak(card.type === 'word' ? card.content : currentCategory.words[card.pairId].word);
 } else {
 if (selectedCard.pairId === card.pairId && selectedCard.id !== card.id) {
 matchedPairs.push(selectedCard.id, card.id);
 matchedCount++;
 celebrate('correct');
 if (matchedCount === 6) {
 saveProgress(currentCategory.id, 'matching', 6, 6);
 setTimeout(() => {
 celebrate('celebrate');
 }, 500);
 }
 } else {
 playSound('wrong');
 speak('Try again!');
 }
 selectedCard = null;
 }
 }
 
 // Quiz mode
 let quizQuestions = $state([]);
 let quizCurrent = $state(0);
 let quizCorrect = $state(0);
 let quizAnswer = $state(null);
 
 function startQuiz() {
 if (!currentCategory) return;
 
 // Shuffle and pick 5 questions
 const shuffled = [...currentCategory.words].sort(() => Math.random() - 0.5).slice(0, 5);
 quizQuestions = shuffled.map(word => {
 // Pick 3 wrong answers
 const others = currentCategory.words.filter(w => w.word !== word.word);
 const wrongAnswers = others.sort(() => Math.random() - 0.5).slice(0, 3);
 const options = [word, ...wrongAnswers].sort(() => Math.random() - 0.5);
 return { word, options, answered: false };
 });
 quizCurrent = 0;
 quizCorrect = 0;
 quizAnswer = null;
 currentView = 'quiz';
 }
 
 function answerQuiz(option, questionIndex) {
 if (quizQuestions[questionIndex].answered) return;
 
 const isCorrect = option.word === quizQuestions[questionIndex].word.word;
 quizQuestions[questionIndex].answered = true;
 quizAnswer = isCorrect ? 'correct' : 'wrong';
 
 if (isCorrect) {
 quizCorrect++;
 celebrate('correct');
 } else {
 playSound('wrong');
 speak(`No, that's ${option.word}. The answer is ${quizQuestions[questionIndex].word.word}`);
 }
 
 setTimeout(() => {
 if (quizCurrent < quizQuestions.length - 1) {
 quizCurrent++;
 quizAnswer = null;
 } else {
 // Quiz complete
 saveProgress(currentCategory.id, 'quiz', quizCorrect, quizQuestions.length);
 if (quizCorrect === quizQuestions.length) {
 celebrate('celebrate');
 }
 }
 }, 1500);
 }
 
 // Tracing mode
 let traceWord = $state(null);
 let tracePoints = $state([]);
 let isDrawing = $state(false);
 
 function startTracing() {
 if (!currentCategory) return;
 traceWord = currentCategory.words[0];
 tracePoints = [];
 currentView = 'tracing';
 }
 
 // Reading section - Mother Goose Nursery Rhymes
 const nurseryRhymes = [
 {
 id: 'jack-jill',
 title: 'Jack and Jill',
 emoji: '🏔️',
 text: `Jack and Jill went up the hill
To fetch a pail of water;
Jack fell down and broke his crown,
And Jill came tumbling after.`,
 illustration: '🧒‍👦💧'
 },
 {
 id: 'little-bo-peep',
 title: 'Little Bo-Peep',
 emoji: '🐑',
 text: `Little Bo-Peep has lost her sheep,
And doesn't know where to find them;
Leave them alone, and they'll come home,
Wagging their tails behind them.`,
 illustration: '👧🐑'
 },
 {
 id: 'humpty-dumpty',
 title: 'Humpty Dumpty',
 emoji: '🥚',
 text: `Humpty Dumpty sat on a wall,
Humpty Dumpty had a great fall;
All the King's horses and all the King's men,
Couldn't put Humpty together again.`,
 illustration: '🥚🏰👑'
 },
 {
 id: 'little-miss-muffet',
 title: 'Little Miss Muffet',
 emoji: '🕷️',
 text: `Little Miss Muffet sat on a tuffet,
Eating her curds and whey;
Along came a spider, who sat down beside her,
And frightened Miss Muffet away.`,
 illustration: '👧🥛🕷️'
 },
 {
 id: 'baa-baa-sheep',
 title: 'Baa, Baa, Black Sheep',
 emoji: '🐑',
 text: `Baa, baa, black sheep,
Have you any wool?
Yes, sir, yes, sir,
Three bags full;
One for the master,
One for the dame,
And one for the little boy
Who lives down the lane.`,
 illustration: '🐑🧶'
 },
 {
 id: 'twinkle-star',
 title: 'Twinkle, Twinkle, Little Star',
 emoji: '⭐',
 text: `Twinkle, twinkle, little star,
How I wonder what you are!
Up above the world so high,
Like a diamond in the sky.`,
 illustration: '✨🌟💎'
 },
 {
 id: 'mary-lamb',
 title: 'Mary Had a Little Lamb',
 emoji: '🐑',
 text: `Mary had a little lamb,
Its fleece was white as snow;
And everywhere that Mary went,
The lamb was sure to go.`,
 illustration: '👧🐑❄️'
 },
 {
 id: 'hickory-dickory',
 title: 'Hickory Dickory Dock',
 emoji: '🐭',
 text: `Hickory, dickory, dock,
The mouse ran up the clock;
The clock struck one,
The mouse ran down,
Hickory, dickory, dock.`,
 illustration: '🐭🕐'
 },
 {
 id: 'hey-diddle',
 title: 'Hey Diddle Diddle',
 emoji: '🐱',
 text: `Hey diddle diddle,
The cat and the fiddle,
The cow jumped over the moon;
The little dog laughed
To see such sport,
And the dish ran away with the spoon.`,
 illustration: '🐱🎻🐄🌙'
 },
 {
 id: 'london-bridge',
 title: 'London Bridge',
 emoji: '🌉',
 text: `London Bridge is falling down,
Falling down, falling down;
London Bridge is falling down,
My fair lady.`,
 illustration: '🌉👑'
 },
 {
 id: 'row-boat',
 title: 'Row, Row, Row Your Boat',
 emoji: '🚣',
 text: `Row, row, row your boat,
Gently down the stream;
Merrily, merrily, merrily, merrily,
Life is but a dream.`,
 illustration: '🚣🌊'
 },
 {
 id: 'old-mcdonald',
 title: 'Old McDonald Had a Farm',
 emoji: '🚜',
 text: `Old McDonald had a farm,
E-I-E-I-O!
And on his farm he had a cow,
E-I-E-I-O!
With a moo-moo here,
And a moo-moo there,
Here a moo, there a moo,
Everywhere a moo-moo!`,
 illustration: '🚜🐄🐷🐔'
 },
 {
 id: 'itsy-bitsy',
 title: 'Itsy Bitsy Spider',
 emoji: '🕷️',
 text: `The itsy bitsy spider
Climbed up the water spout;
Down came the rain
And washed the spider out;
Out came the sun
And dried up all the rain;
And the itsy bitsy spider
Climbed up the spout again.`,
 illustration: '🕷️🌧️☀️'
 },
 {
 id: 'mary-garden',
 title: 'Mary, Mary, Quite Contrary',
 emoji: '🌷',
 text: `Mary, Mary, quite contrary,
How does your garden grow?
With silver bells and cockle shells,
And pretty maids all in a row.`,
 illustration: '👧🌷🌸'
 },
 {
 id: 'jack-horner',
 title: 'Little Jack Horner',
 emoji: '🥧',
 text: `Little Jack Horner
Sat in a corner,
Eating a Christmas pie;
He put in his thumb,
And pulled out a plum,
And said, "What a good boy am I!"`,
 illustration: '👦🥧'
 },
 {
 id: 'jack-sprat',
 title: 'Jack Sprat',
 emoji: '🍽️',
 text: `Jack Sprat could eat no fat,
His wife could eat no lean;
And so between them both, you see,
They licked the platter clean.`,
 illustration: '👨‍👩🍽️'
 },
 {
 id: 'polly-kettle',
 title: 'Polly Put the Kettle On',
 emoji: '☕',
 text: `Polly put the kettle on,
Polly put the kettle on,
Polly put the kettle on,
We'll all have tea.`,
 illustration: '👧☕🍰'
 },
 {
 id: 'rain-rain',
 title: 'Rain, Rain, Go Away',
 emoji: '🌧️',
 text: `Rain, rain, go away,
Come again another day;
Little Johnny wants to play.`,
 illustration: '🌧️👦☀️'
 },
 {
 id: 'star-light',
 title: 'Star Light, Star Bright',
 emoji: '🌟',
 text: `Star light, star bright,
First star I see tonight;
I wish I may, I wish I might,
Have the wish I wish tonight.`,
 illustration: '🌟🌙✨'
 },
 {
 id: 'little-jump',
 title: 'One, Two, Buckle My Shoe',
 emoji: '👟',
 text: `One, two, buckle my shoe;
Three, four, knock at the door;
Five, six, pick up sticks;
Seven, eight, lay them straight;
Nine, ten, a big fat hen!`,
 illustration: '👟🚪🪵🐔'
 }
 ];
 
 let currentRhyme = $state(null);
 let rhymeIndex = $state(0);
 
 function openReading() {
 currentRhyme = nurseryRhymes[0];
 rhymeIndex = 0;
 currentView = 'reading';
 }
 
 function nextRhyme() {
 if (rhymeIndex < nurseryRhymes.length - 1) {
 rhymeIndex++;
 currentRhyme = nurseryRhymes[rhymeIndex];
 }
 }
 
 function prevRhyme() {
 if (rhymeIndex > 0) {
 rhymeIndex--;
 currentRhyme = nurseryRhymes[rhymeIndex];
 }
 }
 
 function selectRhyme(idx) {
 rhymeIndex = idx;
 currentRhyme = nurseryRhymes[idx];
 }
 
 function nextTraceWord() {
 const idx = currentCategory.words.findIndex(w => w.word === traceWord.word);
 if (idx < currentCategory.words.length - 1) {
 traceWord = currentCategory.words[idx + 1];
 tracePoints = [];
 }
 }
 
 function prevTraceWord() {
 const idx = currentCategory.words.findIndex(w => w.word === traceWord.word);
 if (idx > 0) {
 traceWord = currentCategory.words[idx - 1];
 tracePoints = [];
 }
 }
</script>

<main>
 {#if showCelebration}
 <div class="celebration">
 <div class="confetti">🎉⭐🌟✨💫</div>
 <div class="celebrate-text">{celebrationType === 'celebrate' ? 'Amazing!' : 'Great Job!'}</div>
 </div>
 {/if}
 
 <header>
 <button class="home-btn" onclick={goHome}>
 <span class="logo">🧒</span>
 <span class="title">Volta Kids</span>
 </button>
 <div class="score-display">
 <span class="star">⭐</span>
 <span class="score">{score}</span>
 </div>
 </header>
 
 {#if currentView === 'home'}
 <section class="home">
 <h1>Let's Learn English!</h1>
 <p class="subtitle">Choose a topic to start learning</p>
 
 <div class="categories">
 {#each categories as cat}
 <button 
 class="category-btn" 
 style="--cat-color: {cat.color}"
 onclick={() => openCategory(cat)}>
 <span class="cat-emoji">{cat.emoji}</span>
 <span class="cat-name">{cat.name}</span>
 <span class="cat-count">{cat.words.length} words</span>
 {#if getProgressPercent(cat) > 0}
 <div class="progress-bar">
 <div class="progress-fill" style="width: {getProgressPercent(cat)}%"></div>
 </div>
 {/if}
 </button>
 {/each}
 </div>
 
 <div class="reading-section">
 <button class="reading-btn" onclick={openReading}>
 <span class="reading-emoji">📖</span>
 <span class="reading-title">Reading</span>
 <span class="reading-subtitle">Nursery Rhymes</span>
 </button>
 </div>
 </section>
 
 {:else if currentView === 'flashcards'}
 <section class="flashcards">
 <button class="back-btn" onclick={goHome}>← Back</button>
 
 <div class="category-title" style="background: {currentCategory.color}">
 <span class="cat-emoji-large">{currentCategory.emoji}</span>
 <span>{currentCategory.name}</span>
 </div>
 
 <div class="card-container">
 <button class="nav-btn" onclick={prevItem} disabled={currentItem === 0}>
 ◀
 </button>
 
 <div class="flashcard" onclick={() => speak(currentCategory.words[currentItem].word)}>
 <div class="card-emoji">{currentCategory.words[currentItem].emoji}</div>
 <div class="card-word">{currentCategory.words[currentItem].word}</div>
 <div class="card-hint">🔊 Tap to hear</div>
 </div>
 
 <button class="nav-btn" onclick={nextItem} disabled={currentItem === currentCategory.words.length - 1}>
 ▶
 </button>
 </div>
 
 <div class="progress">
 {currentItem + 1} / {currentCategory.words.length}
 </div>
 
 <div class="card-actions">
 <button class="action-btn matching-btn" onclick={startMatching}>
 🎴 Matching
 </button>
 <button class="action-btn quiz-btn" onclick={startQuiz}>
 ❓ Quiz
 </button>
 <button class="action-btn trace-btn" onclick={startTracing}>
 ✏️ Trace
 </button>
 </div>
 </section>
 
 {:else if currentView === 'matching'}
 <section class="matching-game">
 <button class="back-btn" onclick={() => { currentView = 'flashcards'; }}>← Back</button>
 
 <h2>Match the words with pictures!</h2>
 
 <div class="match-grid">
 {#each matchCards as card}
 <button 
 class="match-card {matchedPairs.includes(card.id) ? 'matched' : ''} {selectedCard?.id === card.id ? 'selected' : ''}"
 onclick={() => selectCard(card)}
 disabled={matchedPairs.includes(card.id)}>
 {#if matchedPairs.includes(card.id) || selectedCard?.id === card.id}
 {card.content}
 {:else}
 ❓
 {/if}
 </button>
 {/each}
 </div>
 
 <div class="match-progress">
 Matched: {matchedCount} / 6
 </div>
 </section>
 
 {:else if currentView === 'quiz'}
 <section class="quiz-game">
 <button class="back-btn" onclick={() => { currentView = 'flashcards'; }}>← Back</button>
 
 <div class="quiz-progress">
 Question {quizCurrent + 1} of {quizQuestions.length}
 <div class="quiz-score">Correct: {quizCorrect}</div>
 </div>
 
 {#if quizQuestions[quizCurrent]}
 <div class="quiz-question">
 <div class="quiz-emoji">{quizQuestions[quizCurrent].word.emoji}</div>
 <div class="quiz-prompt">What is this?</div>
 </div>
 
 <div class="quiz-options">
 {#each quizQuestions[quizCurrent].options as opt}
 <button 
 class="quiz-option {quizQuestions[quizCurrent].answered ? (opt.word === quizQuestions[quizCurrent].word.word ? 'correct' : 'wrong') : ''}"
 onclick={() => answerQuiz(opt, quizCurrent)}
 disabled={quizQuestions[quizCurrent].answered}>
 {opt.word}
 </button>
 {/each}
 </div>
 
 {#if quizAnswer === 'correct'}
 <div class="quiz-feedback correct">✓ Correct! 🎉</div>
 {:else if quizAnswer === 'wrong'}
 <div class="quiz-feedback wrong">✗ Try again!</div>
 {/if}
 {/if}
 
 {#if quizCurrent >= quizQuestions.length - 1 && quizQuestions[quizCurrent]?.answered}
 <div class="quiz-complete">
 <h3>Quiz Complete!</h3>
 <p>You got {quizCorrect} out of {quizQuestions.length} correct!</p>
 <button class="action-btn" onclick={() => { currentView = 'flashcards'; }}>Done</button>
 </div>
 {/if}
 </section>
 
 {:else if currentView === 'tracing'}
 <section class="tracing-game">
 <button class="back-btn" onclick={() => { currentView = 'flashcards'; }}>← Back</button>
 
 <div class="tracing-container">
 <div class="tracing-header">
 <button class="nav-btn small" onclick={prevTraceWord} disabled={currentCategory.words.indexOf(traceWord) === 0}>◀</button>
 <div class="trace-word-display">
 <span class="trace-emoji">{traceWord.emoji}</span>
 <span class="trace-word">{traceWord.word}</span>
 </div>
 <button class="nav-btn small" onclick={nextTraceWord} disabled={currentCategory.words.indexOf(traceWord) === currentCategory.words.length - 1}>▶</button>
 </div>
 
 <div class="tracing-canvas">
 <div class="trace-guide">
 {#each traceWord.word.split('') as letter, i}
 <span class="trace-letter">{letter}</span>
 {/each}
 </div>
 <div class="trace-instruction">
 ✏️ Practice writing: {traceWord.word}
 </div>
 <div class="trace-practice">
 <input type="text" class="trace-input" placeholder="Type the word..." />
 </div>
 </div>
 
 <button class="action-btn speak-btn" onclick={() => speak(traceWord.word)}>
 🔊 Hear it
 </button>
 </div>
 </section>
 
 {:else if currentView === 'reading'}
 <section class="reading-view">
 <button class="back-btn" onclick={goHome}>← Back</button>
 
 <div class="rhyme-nav">
 <button class="nav-btn" onclick={prevRhyme} disabled={rhymeIndex === 0}>◀</button>
 <div class="rhyme-counter">{rhymeIndex + 1} / {nurseryRhymes.length}</div>
 <button class="nav-btn" onclick={nextRhyme} disabled={rhymeIndex === nurseryRhymes.length - 1}>▶</button>
 </div>
 
 <div class="rhyme-card">
 <div class="rhyme-header">
 <span class="rhyme-emoji">{currentRhyme.emoji}</span>
 <h2>{currentRhyme.title}</h2>
 </div>
 
 <div class="rhyme-illustration">{currentRhyme.illustration}</div>
 
 <div class="rhyme-text">
 {#each currentRhyme.text.split('\n') as line}
 <p>{line}</p>
 {/each}
 </div>
 
 <div class="rhyme-actions">
 <button class="action-btn speak-btn" onclick={() => speak(currentRhyme.text)}>
 🔊 Read Aloud
 </button>
 </div>
 </div>
 
 <div class="rhyme-list">
 {#each nurseryRhymes as rhyme, idx}
 <button 
 class="rhyme-thumb {idx === rhymeIndex ? 'active' : ''}"
 onclick={() => selectRhyme(idx)}>
 <span class="thumb-emoji">{rhyme.emoji}</span>
 <span class="thumb-title">{rhyme.title}</span>
 </button>
 {/each}
 </div>
 </section>
 {/if}
 
 <footer>
 <p>Volta Kids - Learning is fun! 🌟</p>
 </footer>
</main>

<style>
 :global(*) {
 box-sizing: border-box;
 margin: 0;
 padding: 0;
 }
 
 :global(body) {
 font-family: 'Comic Sans MS', 'Chalkboard', 'Comic Neue', cursive, sans-serif;
 background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
 min-height: 100vh;
 }
 
 main {
 max-width: 800px;
 margin: 0 auto;
 padding: 1rem;
 min-height: 100vh;
 display: flex;
 flex-direction: column;
 }
 
 /* Header */
 header {
 display: flex;
 justify-content: space-between;
 align-items: center;
 padding: 1rem;
 background: rgba(255,255,255,0.95);
 border-radius: 20px;
 margin-bottom: 1.5rem;
 box-shadow: 0 4px 15px rgba(0,0,0,0.2);
 }
 
 .home-btn {
 display: flex;
 align-items: center;
 gap: 0.5rem;
 background: none;
 border: none;
 cursor: pointer;
 font-family: inherit;
 }
 
 .logo {
 font-size: 2rem;
 }
 
 .title {
 font-size: 1.5rem;
 font-weight: bold;
 background: linear-gradient(45deg, #FF6B6B, #4ECDC4);
 -webkit-background-clip: text;
 -webkit-text-fill-color: transparent;
 }
 
 .score-display {
 display: flex;
 align-items: center;
 gap: 0.25rem;
 background: linear-gradient(135deg, #FFE66D, #FFA500);
 padding: 0.5rem 1rem;
 border-radius: 20px;
 font-size: 1.25rem;
 font-weight: bold;
 }
 
 .star {
 font-size: 1.5rem;
 }
 
 /* Home */
 .home {
 text-align: center;
 }
 
 h1 {
 color: white;
 font-size: 2.5rem;
 text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
 margin-bottom: 0.5rem;
 }
 
 .subtitle {
 color: rgba(255,255,255,0.9);
 font-size: 1.25rem;
 margin-bottom: 2rem;
 }
 
 .categories {
 display: grid;
 grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
 gap: 1rem;
 }
 
 .category-btn {
 display: flex;
 flex-direction: column;
 align-items: center;
 gap: 0.5rem;
 padding: 1.5rem 1rem;
 background: white;
 border: none;
 border-radius: 20px;
 cursor: pointer;
 box-shadow: 0 4px 15px rgba(0,0,0,0.2);
 transition: transform 0.2s, box-shadow 0.2s;
 font-family: inherit;
 border-bottom: 5px solid var(--cat-color);
 position: relative;
 }
 
 .category-btn:hover {
 transform: translateY(-5px) scale(1.02);
 box-shadow: 0 8px 25px rgba(0,0,0,0.3);
 }
 
 .category-btn:active {
 transform: scale(0.98);
 }
 
 .cat-emoji {
 font-size: 3rem;
 }
 
 .cat-name {
 font-size: 1.1rem;
 font-weight: bold;
 color: #333;
 }
 
 .cat-count {
 font-size: 0.85rem;
 color: #888;
 }
 
 .progress-bar {
 width: 80%;
 height: 8px;
 background: #eee;
 border-radius: 4px;
 overflow: hidden;
 margin-top: 0.5rem;
 }
 
 .progress-fill {
 height: 100%;
 background: linear-gradient(90deg, #4CAF50, #8BC34A);
 transition: width 0.3s;
 }
 
 /* Flashcards */
 .flashcards {
 flex: 1;
 display: flex;
 flex-direction: column;
 }
 
 .category-title {
 display: flex;
 align-items: center;
 justify-content: center;
 gap: 0.5rem;
 padding: 1rem;
 border-radius: 15px;
 color: white;
 font-size: 1.5rem;
 font-weight: bold;
 margin-bottom: 1rem;
 text-shadow: 1px 1px 2px rgba(0,0,0,0.3);
 }
 
 .cat-emoji-large {
 font-size: 2rem;
 }
 
 .back-btn {
 align-self: flex-start;
 padding: 0.75rem 1.5rem;
 background: rgba(255,255,255,0.9);
 border: none;
 border-radius: 15px;
 font-size: 1.1rem;
 cursor: pointer;
 font-family: inherit;
 margin-bottom: 1rem;
 }
 
 .card-container {
 flex: 1;
 display: flex;
 align-items: center;
 justify-content: center;
 gap: 1rem;
 }
 
 .nav-btn {
 width: 60px;
 height: 60px;
 border-radius: 50%;
 border: none;
 background: rgba(255,255,255,0.9);
 font-size: 1.5rem;
 cursor: pointer;
 box-shadow: 0 4px 10px rgba(0,0,0,0.2);
 font-family: inherit;
 transition: transform 0.2s;
 }
 
 .nav-btn.small {
 width: 40px;
 height: 40px;
 font-size: 1rem;
 }
 
 .nav-btn:hover:not(:disabled) {
 transform: scale(1.1);
 }
 
 .nav-btn:disabled {
 opacity: 0.3;
 cursor: not-allowed;
 }
 
 .flashcard {
 flex: 1;
 max-width: 350px;
 background: white;
 border-radius: 30px;
 padding: 2rem;
 text-align: center;
 cursor: pointer;
 box-shadow: 0 8px 30px rgba(0,0,0,0.3);
 transition: transform 0.2s;
 }
 
 .flashcard:hover {
 transform: scale(1.02);
 }
 
 .card-emoji {
 font-size: 8rem;
 margin-bottom: 1rem;
 }
 
 .card-word {
 font-size: 3rem;
 font-weight: bold;
 color: #333;
 margin-bottom: 0.5rem;
 }
 
 .card-hint {
 font-size: 1rem;
 color: #888;
 }
 
 .progress {
 text-align: center;
 color: white;
 font-size: 1.25rem;
 margin-top: 1rem;
 font-weight: bold;
 }
 
 .card-actions {
 display: flex;
 justify-content: center;
 flex-wrap: wrap;
 gap: 0.75rem;
 margin-top: 1.5rem;
 }
 
 .action-btn {
 padding: 1rem 1.5rem;
 border: none;
 border-radius: 20px;
 font-size: 1.1rem;
 cursor: pointer;
 font-family: inherit;
 font-weight: bold;
 box-shadow: 0 4px 15px rgba(0,0,0,0.2);
 transition: transform 0.2s;
 }
 
 .action-btn:hover {
 transform: scale(1.05);
 }
 
 .matching-btn {
 background: linear-gradient(135deg, #4ECDC4, #44A08D);
 color: white;
 }
 
 .quiz-btn {
 background: linear-gradient(135deg, #9B59B6, #8E44AD);
 color: white;
 }
 
 .trace-btn {
 background: linear-gradient(135deg, #E74C3C, #C0392B);
 color: white;
 }
 
 /* Matching Game */
 .matching-game {
 flex: 1;
 display: flex;
 flex-direction: column;
 align-items: center;
 }
 
 .matching-game h2 {
 color: white;
 text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
 margin-bottom: 1.5rem;
 text-align: center;
 }
 
 .match-grid {
 display: grid;
 grid-template-columns: repeat(4, 1fr);
 gap: 0.75rem;
 width: 100%;
 max-width: 500px;
 }
 
 .match-card {
 aspect-ratio: 1;
 background: white;
 border: none;
 border-radius: 15px;
 font-size: 2rem;
 cursor: pointer;
 box-shadow: 0 4px 15px rgba(0,0,0,0.2);
 transition: transform 0.2s;
 display: flex;
 align-items: center;
 justify-content: center;
 font-family: inherit;
 }
 
 .match-card:hover:not(:disabled) {
 transform: scale(1.05);
 }
 
 .match-card.selected {
 background: #FFE66D;
 transform: scale(1.05);
 }
 
 .match-card.matched {
 background: #A8E6CF;
 cursor: default;
 }
 
 .match-progress {
 margin-top: 1.5rem;
 color: white;
 font-size: 1.25rem;
 font-weight: bold;
 }
 
 /* Quiz Game */
 .quiz-game {
 flex: 1;
 display: flex;
 flex-direction: column;
 align-items: center;
 }
 
 .quiz-progress {
 color: white;
 font-size: 1.25rem;
 margin-bottom: 1.5rem;
 text-align: center;
 }
 
 .quiz-score {
 background: rgba(255,255,255,0.2);
 padding: 0.5rem 1rem;
 border-radius: 10px;
 margin-top: 0.5rem;
 }
 
 .quiz-question {
 background: white;
 border-radius: 30px;
 padding: 2rem;
 text-align: center;
 margin-bottom: 1.5rem;
 box-shadow: 0 8px 30px rgba(0,0,0,0.3);
 }
 
 .quiz-emoji {
 font-size: 6rem;
 margin-bottom: 1rem;
 }
 
 .quiz-prompt {
 font-size: 1.5rem;
 color: #333;
 font-weight: bold;
 }
 
 .quiz-options {
 display: grid;
 grid-template-columns: repeat(2, 1fr);
 gap: 1rem;
 max-width: 400px;
 }
 
 .quiz-option {
 padding: 1.5rem;
 background: white;
 border: none;
 border-radius: 15px;
 font-size: 1.5rem;
 cursor: pointer;
 font-family: inherit;
 font-weight: bold;
 box-shadow: 0 4px 15px rgba(0,0,0,0.2);
 transition: transform 0.2s;
 }
 
 .quiz-option:hover:not(:disabled) {
 transform: scale(1.05);
 }
 
 .quiz-option.correct {
 background: #A8E6CF;
 }
 
 .quiz-option.wrong {
 background: #FF6B6B;
 color: white;
 }
 
 .quiz-feedback {
 font-size: 1.5rem;
 font-weight: bold;
 padding: 1rem 2rem;
 border-radius: 15px;
 margin-top: 1rem;
 }
 
 .quiz-feedback.correct {
 background: #A8E6CF;
 color: #2E7D32;
 }
 
 .quiz-feedback.wrong {
 background: #FFCDD2;
 color: #C62828;
 }
 
 .quiz-complete {
 background: white;
 border-radius: 30px;
 padding: 2rem;
 text-align: center;
 margin-top: 1rem;
 }
 
 .quiz-complete h3 {
 font-size: 2rem;
 color: #333;
 margin-bottom: 1rem;
 }
 
 .quiz-complete p {
 font-size: 1.25rem;
 color: #666;
 margin-bottom: 1rem;
 }
 
 /* Tracing */
 .tracing-game {
 flex: 1;
 display: flex;
 flex-direction: column;
 }
 
 .tracing-container {
 background: white;
 border-radius: 30px;
 padding: 2rem;
 text-align: center;
 box-shadow: 0 8px 30px rgba(0,0,0,0.3);
 }
 
 .tracing-header {
 display: flex;
 align-items: center;
 justify-content: center;
 gap: 1.5rem;
 margin-bottom: 2rem;
 }
 
 .trace-word-display {
 display: flex;
 align-items: center;
 gap: 1rem;
 }
 
 .trace-emoji {
 font-size: 4rem;
 }
 
 .trace-word {
 font-size: 3rem;
 font-weight: bold;
 color: #333;
 }
 
 .tracing-canvas {
 background: #f5f5f5;
 border-radius: 20px;
 padding: 2rem;
 margin-bottom: 1.5rem;
 }
 
 .trace-guide {
 font-size: 4rem;
 letter-spacing: 0.5rem;
 color: #ddd;
 margin-bottom: 1rem;
 }
 
 .trace-letter {
 display: inline-block;
 }
 
 .trace-instruction {
 font-size: 1.25rem;
 color: #666;
 margin-bottom: 1rem;
 }
 
 .trace-practice {
 display: flex;
 justify-content: center;
 }
 
 .trace-input {
 font-size: 2rem;
 padding: 1rem;
 border: 3px dashed #ccc;
 border-radius: 15px;
 text-align: center;
 width: 80%;
 max-width: 300px;
 font-family: inherit;
 }
 
 .trace-input:focus {
 outline: none;
 border-color: #4ECDC4;
 }
 
 .speak-btn {
 background: linear-gradient(135deg, #3498DB, #2980B9);
 color: white;
 margin-top: 1rem;
 }
 
 /* Celebration */
 .celebration {
 position: fixed;
 top: 0;
 left: 0;
 right: 0;
 bottom: 0;
 display: flex;
 flex-direction: column;
 align-items: center;
 justify-content: center;
 background: rgba(0,0,0,0.5);
 z-index: 1000;
 animation: fadeIn 0.3s;
 }
 
 @keyframes fadeIn {
 from { opacity: 0; }
 to { opacity: 1; }
 }
 
 .confetti {
 font-size: 4rem;
 animation: bounce 0.5s infinite alternate;
 }
 
 @keyframes bounce {
 from { transform: scale(1); }
 to { transform: scale(1.2); }
 }
 
 .celebrate-text {
 font-size: 4rem;
 color: white;
 font-weight: bold;
 text-shadow: 3px 3px 6px rgba(0,0,0,0.5);
 animation: pop 0.5s;
 }
 
 @keyframes pop {
 0% { transform: scale(0); }
 50% { transform: scale(1.2); }
 100% { transform: scale(1); }
 }
 
 /* Footer */
 footer {
 text-align: center;
 padding: 1rem;
 color: rgba(255,255,255,0.8);
 margin-top: auto;
 }
 
 /* Responsive */
 @media (max-width: 600px) {
 h1 {
 font-size: 2rem;
 }
 
 .card-emoji, .quiz-emoji {
 font-size: 5rem;
 }
 
 .card-word, .trace-word {
 font-size: 2.5rem;
 }
 
 .match-grid {
 grid-template-columns: repeat(3, 1fr);
 }
 
 .match-card {
 font-size: 1.5rem;
 }
 
 .quiz-options {
 grid-template-columns: 1fr;
 }
 }
 
 /* Reading Section */
 .reading-section {
 margin-top: 2rem;
 display: flex;
 justify-content: center;
 }
 
 .reading-btn {
 display: flex;
 flex-direction: column;
 align-items: center;
 gap: 0.5rem;
 padding: 2rem 3rem;
 background: linear-gradient(135deg, #FF6B6B, #FFA07A);
 border: none;
 border-radius: 25px;
 cursor: pointer;
 box-shadow: 0 8px 25px rgba(0,0,0,0.3);
 transition: transform 0.2s, box-shadow 0.2s;
 font-family: inherit;
 }
 
 .reading-btn:hover {
 transform: translateY(-5px) scale(1.02);
 box-shadow: 0 12px 35px rgba(0,0,0,0.4);
 }
 
 .reading-emoji {
 font-size: 4rem;
 }
 
 .reading-title {
 font-size: 1.75rem;
 font-weight: bold;
 color: white;
 text-shadow: 1px 1px 2px rgba(0,0,0,0.3);
 }
 
 .reading-subtitle {
 font-size: 1rem;
 color: rgba(255,255,255,0.9);
 }
 
 .reading-view {
 flex: 1;
 display: flex;
 flex-direction: column;
 align-items: center;
 padding-bottom: 1rem;
 }
 
 .rhyme-nav {
 display: flex;
 align-items: center;
 gap: 1rem;
 margin-bottom: 1.5rem;
 }
 
 .rhyme-counter {
 color: white;
 font-size: 1.25rem;
 font-weight: bold;
 background: rgba(255,255,255,0.2);
 padding: 0.5rem 1rem;
 border-radius: 10px;
 }
 
 .rhyme-card {
 background: white;
 border-radius: 30px;
 padding: 2rem;
 width: 100%;
 max-width: 600px;
 box-shadow: 0 8px 30px rgba(0,0,0,0.3);
 margin-bottom: 1.5rem;
 }
 
 .rhyme-header {
 display: flex;
 align-items: center;
 justify-content: center;
 gap: 1rem;
 margin-bottom: 1.5rem;
 }
 
 .rhyme-emoji {
 font-size: 3rem;
 }
 
 .rhyme-header h2 {
 font-size: 2rem;
 color: #333;
 margin: 0;
 }
 
 .rhyme-illustration {
 text-align: center;
 font-size: 4rem;
 margin-bottom: 1.5rem;
 padding: 1rem;
 background: linear-gradient(135deg, #FFE66D, #FFA500);
 border-radius: 20px;
 }
 
 .rhyme-text {
 text-align: center;
 font-size: 1.5rem;
 line-height: 2;
 color: #333;
 }
 
 .rhyme-text p {
 margin: 0.75rem 0;
 }
 
 .rhyme-actions {
 display: flex;
 justify-content: center;
 margin-top: 1.5rem;
 }
 
 .rhyme-list {
 display: flex;
 flex-wrap: wrap;
 justify-content: center;
 gap: 0.5rem;
 max-width: 800px;
 }
 
 .rhyme-thumb {
 display: flex;
 flex-direction: column;
 align-items: center;
 gap: 0.25rem;
 padding: 0.5rem 0.75rem;
 background: rgba(255,255,255,0.9);
 border: none;
 border-radius: 15px;
 cursor: pointer;
 font-family: inherit;
 transition: transform 0.2s, background 0.2s;
 box-shadow: 0 2px 8px rgba(0,0,0,0.2);
 }
 
 .rhyme-thumb:hover {
 transform: scale(1.05);
 }
 
 .rhyme-thumb.active {
 background: linear-gradient(135deg, #4ECDC4, #44A08D);
 }
 
 .rhyme-thumb.active .thumb-title {
 color: white;
 }
 
 .thumb-emoji {
 font-size: 1.5rem;
 }
 
 .thumb-title {
 font-size: 0.75rem;
 color: #666;
 font-weight: bold;
 white-space: nowrap;
 }
</style>
