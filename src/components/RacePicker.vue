<script setup>
import { ref, onMounted } from 'vue';

// Reference to the base image element
const baseImageRef = ref(null);

// Original map width for which the coordinates were defined
const originalMapWidth = 800;

// Mouse coordinates
const mouseX = ref(0);
const mouseY = ref(0);
const showCoordinates = ref(false);

// History management
const trackHistory = ref([]);
const showHistoryMenu = ref(false);

// Maximum number of history entries to keep
const MAX_HISTORY_ENTRIES = 20;

// Define the predefined coordinates for each track location (x, y, width, height)
const trackLocations = [
  { x: 190, y: 102, width: 60, height: 60, name: "Bowser\'s Castle" },    //
  { x: 280, y: 108, width: 60, height: 60, name: "Dry Bones Burnout" },   // Dry bones burnout
  { x: 370, y: 60, width: 60, height: 60, name: "Acorn heights" },   // Acorn heights
  { x: 480, y: 99, width: 60, height: 60, name: "Boo Cinema" },   // Boo Cinema

  { x: 567, y: 142, width: 60, height: 60, name: "Starview Peak"},   
  { x: 380, y: 167, width: 60, height: 60, name: "Airship Fortress"},   
  { x: 380, y: 167, width: 60, height: 60, name: "Mario Circuit"},   
  { x: 183, y: 275, width: 60, height: 60, name: "Wario Stadium"},

  { x: 291, y: 220, width: 60, height: 60, name: "Toad\'s Factory"},
  { x: 85, y: 320, width: 60, height: 60, name: "Shy Guy Bazaar"},
  { x: 372, y: 262, width: 60, height: 60, name: "Moo Moo Meadows"},
  { x: 160, y: 385, width: 60, height: 60, name: "Mario Bros. Circuit"},

  { x: 74, y: 460, width: 60, height: 60, name: "Desert Hills"},
  { x: 170, y: 530, width: 60, height: 60, name: "Whistlestop Summit"},
  { x: 275, y: 465, width: 60, height: 60, name: "Crown City"},
  { x: 290, y: 560, width: 60, height: 60, name: "DK Spaceport"},

  { x: 470, y: 230, width: 60, height: 60, name: "Dandelion Depths"},
  { x: 375, y: 375, width: 60, height: 60, name: "Peach Stadium"},
  { x: 475, y: 330, width: 60, height: 60, name: "Cheep Cheep Falls"},
  { x: 375, y: 530, width: 60, height: 60, name: "Koopa Troopa Beach"},

  { x: 655, y: 212, width: 60, height: 60, name: "Sky-High Sundae"},
  { x: 567, y: 274, width: 60, height: 60, name: "DK Pass"},
  { x: 662, y: 353, width: 60, height: 60, name: "Wario Shipyard"},
  { x: 570, y: 396, width: 60, height: 60, name: "Salty Salty Speedway"},

  { x: 495, y: 575, width: 60, height: 60, name: "Dino Dino Jungle"},
  { x: 460, y: 450, width: 60, height: 60, name: "Faraway Oasis"},
  { x: 275, y: 330, width: 60, height: 60, name: "Choco Mountain"},
  { x: 650, y: 460, width: 60, height: 60, name: "Peach Beach"},

  { x: 580, y: 530, width: 60, height: 60, name: "Great ? Block Ruins"},
  { x: 380, y: 470, width: 60, height: 60, name: "Rainbow Road"},
];

const grandPrix = [
  {name: 'Mushroom Cup', icon: 'mushroom.png', track1: "Mario Bros. Circuit", track2: "Crown City", track3: "Whistlestop Summit", track4: "DK Spaceport"},
  {name: 'Flower Cup', icon: 'flower.png', track1: "Desert Hills", track2: "Shy Guy Bazaar", track3: "Wario Stadium", track4: "Airship Fortress"},
  // {name: 'Star Cup', icon: 'star.png'},
  // {name: 'Shell Cup', icon: 'shell.png'},
  // {name: 'Banana Cup', icon: 'banana.png'},
  // {name: 'Leaf Cup', icon: 'leaf.png'},
  // {name: 'Lightning Cup', icon: 'lightning.png'},
  // {name: 'Special Cup', icon: 'Special.png'},
]

// Store the selected cup for display
const selectedCup = ref(null);

const trackMapLoaded = ref(false);
const highlightStyle = ref({
  display: 'none',
  left: '0px',
  top: '0px',
  width: '0px',
  height: '0px',
  opacity: 1
});

// Array to store multiple highlights for the 4-track picker
const multipleHighlights = ref([]);

// Store the selected track name for single track selection
const selectedTrackName = ref('');

function handleImageLoad(event) {
  // Image is loaded, set trackMapLoaded to true
  trackMapLoaded.value = true;
}

function handleMouseMove(event) {
  if (baseImageRef.value) {
    // Get the bounding rectangle of the image
    const rect = baseImageRef.value.getBoundingClientRect();
    
    // Calculate mouse position relative to the image
    const x = event.clientX - rect.left;
    const y = event.clientY - rect.top;
    
    // Calculate the scale factor based on the actual rendered width
    const actualWidth = baseImageRef.value.offsetWidth;
    const scaleFactor = originalMapWidth / actualWidth;
    
    // Convert to original image coordinates
    mouseX.value = Math.round(x * scaleFactor);
    mouseY.value = Math.round(y * scaleFactor);
  }
}

function handleMouseEnter() {
  showCoordinates.value = true;
}

function handleMouseLeave() {
  showCoordinates.value = false;
}

function pickRandomTrack() {
  // Pick a random location from the predefined coordinates
  const randomIndex = Math.floor(Math.random() * trackLocations.length);
  const selectedLocation = trackLocations[randomIndex];
  
  // Store the selected track name
  selectedTrackName.value = selectedLocation.name;
  
  // Calculate the scale factor based on the actual rendered width
  const actualWidth = baseImageRef.value ? baseImageRef.value.offsetWidth : originalMapWidth;
  const scaleFactor = actualWidth / originalMapWidth;
  
  // Scale the coordinates based on the actual size of the map
  const scaledX = selectedLocation.x * scaleFactor;
  const scaledY = selectedLocation.y * scaleFactor;
  const scaledWidth = selectedLocation.width * scaleFactor;
  const scaledHeight = selectedLocation.height * scaleFactor;
  
  // Clear any previous multiple highlights
  multipleHighlights.value = [];
  
  // Set the highlighter position and size using the scaled coordinates
  highlightStyle.value = {
    left: scaledX + 'px',
    top: scaledY + 'px',
    width: scaledWidth + 'px',
    height: scaledHeight + 'px',
    display: 'block',
    opacity: 0,
    transition: 'none',
    borderWidth: '5px',
    // Use a special style for single race selection
    backgroundColor: 'rgba(255, 230, 0, 0.25)',
    borderRadius: '12px'
  };
  
  // Add animation effect
  setTimeout(() => {
    highlightStyle.value = {
      ...highlightStyle.value,
      opacity: 1,
      transition: 'opacity 0.5s, transform 0.3s'
    };
  }, 10);
  
  // Add to history
  addToHistory('single', selectedLocation);
}

function pickFourRandomTracks() {
  // Clear any previous highlights
  highlightStyle.value.display = 'none';
  multipleHighlights.value = [];
  // Clear the selected track name for single track selection
  selectedTrackName.value = '';
  
  // Create a copy of indices to select from
  const availableIndices = Array.from({ length: trackLocations.length }, (_, i) => i);
  const selectedIndices = [];
  
  // Select 4 unique random indices
  for (let i = 0; i < 4; i++) {
    // If we have fewer than 4 track locations, break out of the loop
    if (availableIndices.length === 0) break;
    
    // Pick a random index from the available indices
    const randomPosition = Math.floor(Math.random() * availableIndices.length);
    const selectedIndex = availableIndices.splice(randomPosition, 1)[0];
    selectedIndices.push(selectedIndex);
  }
  
  // Calculate the scale factor based on the actual rendered width
  const actualWidth = baseImageRef.value ? baseImageRef.value.offsetWidth : originalMapWidth;
  const scaleFactor = actualWidth / originalMapWidth;
  
  // Store selected tracks for history
  const selectedTracks = selectedIndices.map(index => trackLocations[index]);
  
  // Create highlight styles for each selected track
  selectedIndices.forEach((index, i) => {
    const selectedLocation = trackLocations[index];
    
    // Scale the coordinates based on the actual size of the map
    const scaledX = selectedLocation.x * scaleFactor;
    const scaledY = selectedLocation.y * scaleFactor;
    const scaledWidth = selectedLocation.width * scaleFactor;
    const scaledHeight = selectedLocation.height * scaleFactor;
    
    // Add to the multiple highlights array with a delay for animation
    setTimeout(() => {
      multipleHighlights.value.push({
        id: `highlight-${index}`,
        trackNumber: i + 1, // Add track number (1-4)
        numberColor: getTrackNumberColor(i), // Add color for the track number
        trackName: selectedLocation.name, // Add track name
        style: {
          left: scaledX + 'px',
          top: scaledY + 'px',
          width: scaledWidth + 'px',
          height: scaledHeight + 'px',
          opacity: 1,
          transition: 'opacity 0.5s, transform 0.3s',
          borderColor: getHighlightColor(i),
          borderWidth: '5px',
          animationDelay: `${i * 0.2}s`
        }
      });
      
      // Add to history after all highlights are added
      if (i === selectedIndices.length - 1) {
        addToHistory('multiple', selectedTracks);
      }
    }, i * 300); // Stagger the appearance of highlights
  });
}

// Function to get different colors for each highlight
function getHighlightColor(index) {
  const colors = ['red', '#00cc00', '#0066ff', '#ffcc00']; // Red, Green, Blue, Yellow
  return colors[index % colors.length];
}

// Function to match track number color with highlight color for consistency
function getTrackNumberColor(index) {
  // Use slightly darker versions of the highlight colors for better contrast
  const colors = ['#cc0000', '#009900', '#0055cc', '#e6b800']; // Darker Red, Green, Blue, Yellow
  return colors[index % colors.length];
}

// Apply a historical track selection
function applyHistoryEntry(entry) {
  if (entry.type === 'single') {
    // For single track, apply the track directly
    applySingleTrack(entry.tracks);
  } else {
    // For multiple tracks, apply all tracks
    applyMultipleTracks(entry.tracks);
  }
}

// Apply a single track from history
function applySingleTrack(track) {
  // Store the selected track name
  selectedTrackName.value = track.name;
  
  // Calculate the scale factor based on the actual rendered width
  const actualWidth = baseImageRef.value ? baseImageRef.value.offsetWidth : originalMapWidth;
  const scaleFactor = actualWidth / originalMapWidth;
  
  // Scale the coordinates based on the actual size of the map
  const scaledX = track.x * scaleFactor;
  const scaledY = track.y * scaleFactor;
  const scaledWidth = track.width * scaleFactor;
  const scaledHeight = track.height * scaleFactor;
  
  // Clear any previous multiple highlights
  multipleHighlights.value = [];
  
  // Set the highlighter position and size using the scaled coordinates
  highlightStyle.value = {
    left: scaledX + 'px',
    top: scaledY + 'px',
    width: scaledWidth + 'px',
    height: scaledHeight + 'px',
    display: 'block',
    opacity: 1,
    transition: 'opacity 0.5s, transform 0.3s',
    borderWidth: '5px',
    backgroundColor: 'rgba(255, 230, 0, 0.25)',
    borderRadius: '12px'
  };
}

// Apply multiple tracks from history
function applyMultipleTracks(tracks) {
  // Clear any previous highlights
  highlightStyle.value.display = 'none';
  multipleHighlights.value = [];
  // Clear the selected track name for single track selection
  selectedTrackName.value = '';
  
  // Calculate the scale factor based on the actual rendered width
  const actualWidth = baseImageRef.value ? baseImageRef.value.offsetWidth : originalMapWidth;
  const scaleFactor = actualWidth / originalMapWidth;
  
  // Create highlight styles for each selected track
  tracks.forEach((track, i) => {
    // Scale the coordinates based on the actual size of the map
    const scaledX = track.x * scaleFactor;
    const scaledY = track.y * scaleFactor;
    const scaledWidth = track.width * scaleFactor;
    const scaledHeight = track.height * scaleFactor;
    
    // Add to the multiple highlights array
    multipleHighlights.value.push({
      id: `highlight-${i}`,
      trackNumber: i + 1,
      numberColor: getTrackNumberColor(i),
      trackName: track.name,
      style: {
        left: scaledX + 'px',
        top: scaledY + 'px',
        width: scaledWidth + 'px',
        height: scaledHeight + 'px',
        opacity: 1,
        transition: 'opacity 0.5s, transform 0.3s',
        borderColor: getHighlightColor(i),
        borderWidth: '5px',
        animationDelay: `${i * 0.2}s`
      }
    });
  });
}

// Load track history from local storage
function loadTrackHistory() {
  const savedHistory = localStorage.getItem('trackHistory');
  if (savedHistory) {
    try {
      trackHistory.value = JSON.parse(savedHistory);
    } catch (e) {
      console.error('Failed to parse track history from local storage:', e);
      trackHistory.value = [];
    }
  }
}

// Save track history to local storage
function saveTrackHistory() {
  localStorage.setItem('trackHistory', JSON.stringify(trackHistory.value));
}

// Add a track selection to history
function addToHistory(type, tracks, cup = null) {
  // Create a new history entry
  const historyEntry = {
    id: Date.now(), // Unique ID based on timestamp
    timestamp: new Date().toLocaleString(), // Human-readable date and time
    type: type, // 'single' or 'multiple'
    tracks: tracks, // Array of track objects or single track object
    cup: cup // Cup information (if a random cup was selected)
  };
  
  // Add to the beginning of the history array
  trackHistory.value.unshift(historyEntry);
  
  // Limit the number of history entries
  if (trackHistory.value.length > MAX_HISTORY_ENTRIES) {
    trackHistory.value = trackHistory.value.slice(0, MAX_HISTORY_ENTRIES);
  }
  
  // Save to local storage
  saveTrackHistory();
}

// Toggle history menu visibility
function toggleHistoryMenu() {
  showHistoryMenu.value = !showHistoryMenu.value;
}

// Clear track history
function clearHistory() {
  trackHistory.value = [];
  saveTrackHistory();
}

// Function to pick a random cup and highlight its 4 tracks
function pickRandomCup() {
  // Clear any previous highlights
  highlightStyle.value.display = 'none';
  multipleHighlights.value = [];
  // Clear the selected track name for single track selection
  selectedTrackName.value = '';
  
  // Get active cups (those that have track1-track4 defined)
  const activeCups = grandPrix.filter(cup => cup.track1 && cup.track2 && cup.track3 && cup.track4);
  
  // If no active cups, return
  if (activeCups.length === 0) return;
  
  // Pick a random cup
  const randomIndex = Math.floor(Math.random() * activeCups.length);
  const selectedCupData = activeCups[randomIndex];
  
  // Store the selected cup for display
  selectedCup.value = selectedCupData;
  
  // Get the track locations for the 4 tracks in the cup
  const cupTracks = [
    selectedCupData.track1,
    selectedCupData.track2,
    selectedCupData.track3,
    selectedCupData.track4
  ];
  
  // Find the track locations for each track name
  const selectedTracks = cupTracks.map(trackName => {
    return trackLocations.find(location => location.name === trackName) || null;
  }).filter(track => track !== null); // Filter out any tracks that weren't found
  
  // If we don't have any valid tracks, return
  if (selectedTracks.length === 0) return;
  
  // Calculate the scale factor based on the actual rendered width
  const actualWidth = baseImageRef.value ? baseImageRef.value.offsetWidth : originalMapWidth;
  const scaleFactor = actualWidth / originalMapWidth;
  
  // Create highlight styles for each selected track
  selectedTracks.forEach((track, i) => {
    // Scale the coordinates based on the actual size of the map
    const scaledX = track.x * scaleFactor;
    const scaledY = track.y * scaleFactor;
    const scaledWidth = track.width * scaleFactor;
    const scaledHeight = track.height * scaleFactor;
    
    // Add to the multiple highlights array with a delay for animation
    setTimeout(() => {
      multipleHighlights.value.push({
        id: `highlight-${i}`,
        trackNumber: i + 1, // Add track number (1-4)
        numberColor: getTrackNumberColor(i), // Add color for the track number
        trackName: track.name, // Add track name
        style: {
          left: scaledX + 'px',
          top: scaledY + 'px',
          width: scaledWidth + 'px',
          height: scaledHeight + 'px',
          opacity: 1,
          transition: 'opacity 0.5s, transform 0.3s',
          borderColor: getHighlightColor(i),
          borderWidth: '5px',
          animationDelay: `${i * 0.2}s`
        }
      });
      
      // Add to history after all highlights are added
      if (i === selectedTracks.length - 1) {
        addToHistory('multiple', selectedTracks, selectedCupData);
      }
    }, i * 300); // Stagger the appearance of highlights
  });
}

// Load history when component is mounted
onMounted(() => {
  loadTrackHistory();
});
</script>

<template>
  <div class="track-picker">
    <h1>Mario Kart à Boire - Random Track Picker</h1>
    
    <div 
      class="image-container"
      @mousemove="handleMouseMove"
      @mouseenter="handleMouseEnter"
      @mouseleave="handleMouseLeave"
    >
      <!-- Cup logo display -->
      <div v-if="selectedCup" class="cup-logo-container">
        <img :src="`/GrandPrix/${selectedCup.icon}`" :alt="`${selectedCup.name} logo`" class="cup-logo">
        <div class="cup-name">{{ selectedCup.name }}</div>
      </div>
      
      <div 
        v-if="showCoordinates" 
        class="coordinates-label"
      >
        X: {{ mouseX }}, Y: {{ mouseY }}
      </div>
      <img 
        ref="baseImageRef"
        src="/MarioKartWorld_World_Map_Inner.webp" 
        alt="Mario Kart Wii World Map" 
        @load="handleImageLoad"
        class="base-image"
      >
      <img 
        src="/MarioKartWorld_World_Map_Stages.webp" 
        alt="Mario Kart Wii Tracks Overlay" 
        class="overlay-image"
      >
      <div 
        class="highlight" 
        :style="highlightStyle"
      >
        <div v-if="selectedTrackName && highlightStyle.display !== 'none'" class="track-name">{{ selectedTrackName }}</div>
      </div>
      <div 
        v-for="highlight in multipleHighlights" 
        :key="highlight.id"
        class="highlight multiple-highlight" 
        :style="highlight.style"
      >
        <div class="track-number" :style="{ backgroundColor: highlight.numberColor, borderColor: highlight.style.borderColor }">{{ highlight.trackNumber }}</div>
        <div class="track-name">{{ highlight.trackName }}</div>
      </div>
    </div>
    
    <div class="button-container">
      <button @click="pickRandomTrack">Pick Random Track</button>
      <button @click="pickFourRandomTracks" class="four-tracks-button">Pick 4 Random Tracks</button>
      <button @click="pickRandomCup" class="cup-button">Pick Random Cup</button>
      <button @click="toggleHistoryMenu" class="history-button">
        <span v-if="!showHistoryMenu">Show History</span>
        <span v-else>Hide History</span>
      </button>
    </div>
    
    <!-- History Sliding Menu -->
    <div class="history-menu" :class="{ 'show': showHistoryMenu }">
      <div class="history-header">
        <h2>Track History</h2>
        <button @click="clearHistory" class="clear-history-button">Clear History</button>
      </div>
      
      <div class="history-list">
        <div v-if="trackHistory.length === 0" class="no-history">
          No track history yet. Pick some tracks!
        </div>
        
        <div 
          v-for="entry in trackHistory" 
          :key="entry.id"
          class="history-entry"
          @click="applyHistoryEntry(entry)"
        >
          <div class="history-timestamp">{{ entry.timestamp }}</div>
          <div class="history-type">
            {{ entry.type === 'single' ? 'Single Track' : (entry.cup ? `Cup: ${entry.cup.name}` : '4 Tracks') }}
          </div>
          <div class="history-tracks">
            <div v-if="entry.type === 'single'">
              {{ entry.tracks.name }}
            </div>
            <div v-else class="multiple-tracks-list">
              <div v-for="(track, index) in entry.tracks" :key="index" class="history-track-item">
                {{ index + 1 }}. {{ track.name }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.track-picker {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  position: relative;
  overflow-x: hidden; /* Prevent horizontal scrolling when menu slides in */
}

.image-container {
  position: relative;
  margin-bottom: 20px;
  width: 90vw;
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;
}

img {
  width: 100%;
  height: auto;
  display: block;
}

.base-image {
  position: relative;
  z-index: 1;
}

.overlay-image {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 2;
}

.highlight {
  position: absolute;
  border: 4px dashed;
  border-image: repeating-linear-gradient(45deg, #000, #000 10px, #fff 10px, #fff 20px) 1;
  box-shadow: 0 0 10px rgba(255, 255, 255, 0.7), inset 0 0 20px rgba(255, 255, 255, 0.5);
  background-color: rgba(0, 162, 255, 0.2);
  border-radius: 10px;
  pointer-events: none;
  z-index: 3;
  animation: pulse 1.5s infinite alternate;
}

@keyframes pulse {
  from {
    box-shadow: 0 0 10px rgba(255, 255, 255, 0.7), inset 0 0 20px rgba(255, 255, 255, 0.5);
  }
  to {
    box-shadow: 0 0 20px rgba(255, 255, 255, 0.9), inset 0 0 30px rgba(255, 255, 255, 0.7);
  }
}

.coordinates-label {
  position: absolute;
  top: 10px;
  left: 10px;
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 5px 10px;
  border-radius: 4px;
  font-size: 14px;
  z-index: 4;
  pointer-events: none;
}

.button-container {
  display: flex;
  gap: 20px;
  margin-top: 20px;
  flex-wrap: wrap;
  justify-content: center;
}

button {
  padding: 10px 20px;
  font-size: 18px;
  background-color: #e60012;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #ff3c4a;
}

.four-tracks-button {
  background-color: #0066cc;
}

.four-tracks-button:hover {
  background-color: #0088ff;
}

.history-button {
  background-color: #ff9900;
}

.history-button:hover {
  background-color: #ffb340;
}

.cup-button {
  background-color: #009933;
}

.cup-button:hover {
  background-color: #00cc44;
}

.multiple-highlight {
  /* The border color is set dynamically in the component */
  background-color: rgba(255, 255, 255, 0.25);
  /* Override the border-image for multiple highlights to use the border-color */
  border-image: none;
  /* Make the border dashed for a more game-like appearance */
  border-style: dashed;
  /* Slightly different animation for multiple highlights */
  animation: float 2s infinite alternate;
}

@keyframes float {
  from {
    transform: translateY(0);
    box-shadow: 0 0 10px rgba(255, 255, 255, 0.7), inset 0 0 20px rgba(255, 255, 255, 0.5);
  }
  to {
    transform: translateY(-5px);
    box-shadow: 0 0 20px rgba(255, 255, 255, 0.9), inset 0 0 30px rgba(255, 255, 255, 0.7);
  }
}

.track-number {
  position: absolute;
  top: -15px;
  right: -15px;
  width: 30px;
  height: 30px;
  background-color: #ffcc00; /* Default color, will be overridden by inline style */
  border: 3px solid #000; /* Default color, will be overridden by inline style */
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 18px;
  font-weight: bold;
  color: #fff; /* White text for better contrast on colored backgrounds */
  box-shadow: 0 0 10px rgba(255, 255, 255, 0.9);
  text-shadow: 1px 1px 0 #000, -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000; /* Dark outline for better visibility */
  animation: bounce 0.6s infinite alternate;
  z-index: 5;
  /* Ensure the track number is visible on all screen sizes */
  transform-origin: center;
  pointer-events: none;
}

@keyframes bounce {
  from {
    transform: scale(1);
  }
  to {
    transform: scale(1.1);
  }
}

.track-name {
  position: absolute;
  bottom: -30px;
  left: 50%;
  transform: translateX(-50%);
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 4px 10px;
  border-radius: 8px;
  font-size: 14px;
  font-weight: bold;
  white-space: nowrap;
  text-align: center;
  box-shadow: 0 0 8px rgba(255, 255, 255, 0.6);
  border: 2px solid #ffcc00;
  text-shadow: 1px 1px 2px #000;
  z-index: 5;
  pointer-events: none;
  animation: glow 1.5s infinite alternate;
}

@keyframes glow {
  from {
    box-shadow: 0 0 8px rgba(255, 255, 255, 0.6);
  }
  to {
    box-shadow: 0 0 15px rgba(255, 255, 255, 0.9);
  }
}

/* History Menu Styles */
.history-menu {
  position: fixed;
  top: 0;
  right: -350px; /* Start off-screen */
  width: 350px;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.85);
  z-index: 10;
  transition: right 0.3s ease-in-out;
  box-shadow: -5px 0 15px rgba(0, 0, 0, 0.5);
  border-left: 4px solid #ffcc00;
  overflow-y: auto; /* Enable scrolling for long lists */
  padding: 20px;
  color: white;
  font-family: 'Arial', sans-serif;
}

.history-menu.show {
  right: 0; /* Slide in */
}

.history-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding-bottom: 10px;
  border-bottom: 2px dashed #ffcc00;
}

.history-header h2 {
  margin: 0;
  color: #ffcc00;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.7);
  font-size: 24px;
}

.clear-history-button {
  background-color: #e60012;
  padding: 5px 10px;
  font-size: 14px;
}

.history-list {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.no-history {
  text-align: center;
  padding: 20px;
  color: #aaa;
  font-style: italic;
}

.history-entry {
  background-color: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  padding: 12px;
  cursor: pointer;
  transition: all 0.2s;
  border: 2px solid transparent;
  position: relative;
  overflow: hidden;
}

.history-entry:hover {
  background-color: rgba(255, 255, 255, 0.2);
  border-color: #ffcc00;
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.history-entry:active {
  transform: translateY(0);
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
}

.history-timestamp {
  font-size: 12px;
  color: #aaa;
  margin-bottom: 5px;
}

.history-type {
  font-weight: bold;
  color: #ffcc00;
  margin-bottom: 8px;
  font-size: 16px;
}

.history-tracks {
  font-size: 14px;
}

.multiple-tracks-list {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.history-track-item {
  padding-left: 10px;
  position: relative;
}

.history-track-item:before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 5px;
  height: 5px;
  background-color: #ffcc00;
  border-radius: 50%;
}

/* Cup logo styling */
.cup-logo-container {
  position: absolute;
  top: 10px;
  left: 10px;
  z-index: 5;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.7);
  border-radius: 10px;
  padding: 10px;
  border: 3px solid #ffcc00;
  box-shadow: 0 0 15px rgba(255, 255, 255, 0.7);
  animation: glow 1.5s infinite alternate;
}

.cup-logo {
  width: 80px;
  height: 80px;
  object-fit: contain;
}

.cup-name {
  color: white;
  font-weight: bold;
  margin-top: 5px;
  text-align: center;
  text-shadow: 1px 1px 2px #000;
  font-size: 14px;
}
</style>