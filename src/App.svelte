<script>
 import { onMount } from 'svelte';
 
 let currentView = $state('home');
 let currentCategory = $state(null);
 let currentItem = $state(0);
 let score = $state(0);
 let showCelebration = $state(false);
 let matchedPairs = $state([]);
 let selectedCard = $state(null);
 
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
 }
 ];
 
 let synth = null;
 let voices = [];
 
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
 });
 
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
 
 function celebrate() {
 showCelebration = true;
 score += 5;
 localStorage.setItem('volta-kids-score', score);
 setTimeout(() => { showCelebration = false; }, 2000);
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
 
 // Create pairs: word cards and emoji cards
 const cards = [];
 currentCategory.words.slice(0, 6).forEach((w, i) => {
 cards.push({ id: i, type: 'word', content: w.word, pairId: i });
 cards.push({ id: i + 100, type: 'emoji', content: w.emoji, pairId: i });
 });
 
 // Shuffle
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
 // Match!
 matchedPairs.push(selectedCard.id, card.id);
 matchedCount++;
 celebrate();
 if (matchedCount === 6) {
 setTimeout(() => {
 alert('🎉 Amazing! You matched all the cards!');
 }, 500);
 }
 } else {
 // No match - briefly show then flip back
 speak('Try again!');
 }
 selectedCard = null;
 }
 }
</script>

<main>
 {#if showCelebration}
 <div class="celebration">
 <div class="confetti">🎉⭐🌟✨💫</div>
 <div class="celebrate-text">Great Job!</div>
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
 </button>
 {/each}
 </div>
 </section>
 
 {:else if currentView === 'flashcards'}
 <section class="flashcards">
 <button class="back-btn" onclick={goHome}>← Back</button>
 
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
 <button class="action-btn play-btn" onclick={startMatching}>
 🎴 Play Matching Game
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
 
 /* Flashcards */
 .flashcards {
 flex: 1;
 display: flex;
 flex-direction: column;
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
 gap: 1rem;
 margin-top: 1.5rem;
 }
 
 .action-btn {
 padding: 1rem 2rem;
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
 
 .play-btn {
 background: linear-gradient(135deg, #4ECDC4, #44A08D);
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
 
 .card-emoji {
 font-size: 5rem;
 }
 
 .card-word {
 font-size: 2.5rem;
 }
 
 .match-grid {
 grid-template-columns: repeat(3, 1fr);
 }
 
 .match-card {
 font-size: 1.5rem;
 }
 }
</style>
